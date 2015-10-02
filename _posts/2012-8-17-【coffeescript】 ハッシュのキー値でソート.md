---
layout: post
title: 【coffeescript】 ハッシュのキー値でソート
---


配列のソートはsort()、でOKだけど、ハッシュキーでのソートを実現したかったのでやってみました。  
  
```javascript
miku =
  m: 39
  i: 23
  k: 94
  u: 12
  a: 61
  b: 33
  c: 24
  d: 58

keySort = (hash, sort) ->
  sortFunc = sort or "sort"
  keys = []
  newHash = {}
  for k of hash
    keys.push k
  keys[sortFunc]()
  length = keys.length
  i = 0


  while i < length
    newHash[keys[i]] = hash[keys[i]]
    i++
  newHash

miku = keySort(hash)
miku = keySort(hash, "reverse")
```
  
以上です。
