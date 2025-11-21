---
title: Vueのwatchで同時に複数の値を監視するTips
tags:
  - JavaScript
  - Vue.js
private: false
updated_at: '2021-02-17T21:06:46+09:00'
id: c2cf406b4787e0b32dec
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
最近Vueを触ることがあって、面白いなと思ったので紹介します。Vueをあまり使い慣れていないので、書き方間違っていたらごめんなさい


## 実際の例

下のようにwatchで`hoge`と`fuga`という値を監視していて、それぞれが更新された時に同じ処理をする場合があります。

```vuejs
// hogeとfugaで同じ処理

watch: {
 hoge() {
  // 何らかの処理
 },
 fuga() {
  // hogeと同じ何らかの処理
 },
}
```

このとき、以下のようにして、2つの処理をまとめることができます。

```vue
computed: {
 hoge_fuga() {
  return `${this.hoge}_${this.fuga}`;
 }
},
watch: {
 hoge_fuga() {
  // 何らかの処理
 }
}

```

hogeとfugaのどちらかが変更されると、computedに定義した`hoge_fuga`が更新され、`hoge_fuga`をwatchで監視しているので処理が実行されます。
とても単純ですが、面白いなと思いました。


## 最後に
読んでいただきありがとうございます！少しでも役立ったら嬉しいです！



