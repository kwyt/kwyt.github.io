---
layout: post
title: 【coffeescript】クラス内でsetInterval
---


クラスのメソッドでsetIntervalを使って処理を繰り返そうと思い、単純に  
  
```c
class Test

  getData:->
    console.log "get Data"

  setUpdateInterval:->
    setInterval this.getData(), 3000
```
  
的なコードを書きました。  
しかしこの場合、setIntervalは動作せず、getDataが一回呼び出されて処理が終わってしまいました。  
  
setInterval()はデフォルトではwindowオブジェクトに設定されているため、クラスのメソッドを使用するときは、  
明示的に、コールバック関数にthisをbindして与えなくてはいなけいみたいです。  
  
以下が正しく動いたコードです。  
  
```c
class Test

  getData: ->
    console.log "get Data"

  setUpdateInterval: (test) ->
    callback = @getData.bind this
    setInterval callback, 3000
```
  
結構詰まってしまいました＾＾;  
以上です。
