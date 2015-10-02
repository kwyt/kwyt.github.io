---
layout: post
title: 【coffeescript】 関数の戻り値
---


coffeescriptで書いたコードをjavascriptにコンパイルした際に、定義した関数で最後の一行が評価され返ってきて困っていた。コンパイルする度にそこだけjavascriptを書き直すっていう愚行を何回かしていた。  
で、一度coffeescriptの基本構文を読み返していたら、関数の戻り値は、関数の最後に評価された式の結果になるから、明示的にreturnを書かないといけないってことが書いてあった＾＾；  
スコープだけしかっり見とけばいいかー、と、他はさらっと読み飛ばしていたからこんなことになってしまったんですねー。。反省。

```javascript
miku ->
  type = 'vocaloid'
  ver = 2
  age = 16
  return

↓コンパイル結果↓
miku(function() {
  var age, type, ver;
  type = 'vocaloid';
  ver = 2;
  age = 16;
});

参考・・・returnなしの場合
miku(function() {
  var age, type, ver;
  type = "vocaloid";
  ver = 2;
  return age = 16;
});
```
