---
layout: post
title: 【ruby】 websocketで特定の相手にだけメッセージの送信
---

rubyのwebsocketライブラリである、em-websocketを使って特定の相手とメッセージをやりとりする実装方法です。  
サーバー側はクライアントから送信されるメッセージの配送的な役割をします。それなので、リアルタイム通信を用いたアプリケーションでは、クライアント分散方式を採用するのが有効です。  
  
em-websocketはeventmachineベースで実装されていてシングルスレッドで動作します。  
今回の実装方法ではマルチコアスケールは考慮されていません。  
マルチコアで負荷分散させたい場合は、複数プロセス起動させるなどしないと実現できません。  
  
以下実装コードです。
  
```ruby
require 'rubygems'
require 'em-websocket'
require 'eventmachine'
require 'json'
require 'msgpack'


Process.daemon(nochdir=true) if ARGV[0] == '-d'

@channels = {}
@connection_length_info = Hash.new(0)
@connections = []
@mutex = Mutex.new

EM.epoll
EM.set_descriptor_table_size 60000

EM.run do
  EM::WebSocket.start(host: '0.0.0.0', port: 9001, debug: true) do |socket|
    socket.onopen do |handshake|
      response = {}

      sid = channel_for_socket(handshake).subscribe do |message|

        if sid != @con_id || @is_onopen
          socket.send_binary message
          @is_onopen = false
        end

      end

      @connection_length_info["#{@path}"] += 1
      @is_onopen = true

      response[:socket_server] = 'connected'
      response[:connection_count] = @connection_length_info["#{@path}"]
      channel_for_socket(handshake).push response.to_msgpack

      socket.onbinary do |data|
         @con_id = sid
         channel_for_socket(handshake).push data
      end

      socket.onclose do
        @connection_length_info["#{@path}"] -= 1

        response = {}
        response[:socket_server] = 'disconnected'
        response[:connection_count] = @connection_length_info["#{@path}"]

        channel_for_socket(handshake).push response.to_msgpack
        channel_for_socket(handshake).unsubscribe sid
        delete_connection_data
      end
    end

    socket.onerror do |error|
      p error.message
    end
  end

  def channel_for_socket socket
    if socket.query
      @path = socket.path
    else
      p 'connection error'
    end

    @mutex.synchronize do
      @channels[@path] ||= EM::Channel.new
    end

  end

  def delete_connection_data
   @connection_length_info.delete(@path) if @connection_length_info["#{@path}"] == 0
   end
end
```
  
接続状態の管理はURLのパスとEM::Channelをハッシュで管理していて、メッセージ送信時に、同じパスに接続しているユーザーにだけメッセージがいくようにしています。  
また、送受信メッセージの軽量化をするためにmsgpackを使用しています。  
  
プロセス単体でのベンチマークでは同時2万接続まで成功しました。
