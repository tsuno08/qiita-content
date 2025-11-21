---
title: トリボナッチ数列のn番目を求める関数をメモ化してみた！(JavaScript)
tags:
  - JavaScript
  - algorithm
  - Jest
private: false
updated_at: '2020-11-27T23:18:26+09:00'
id: e46bdd330c2ea1aef5de
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
最近、Udemyでアルゴリズムの講座とかをやってたりするのですが、JavaScriptでフィボナッチ数列のプログラムを書いて、それをメモ化するのが面白かったので、トリボナッチ数列でも少し変えてやってみました。

Stephen Grider先生の講座はどれも分かりやすいです。
参考：[The Coding Interview Bootcamp: Algorithms + Data Structures
](https://www.udemy.com/course/coding-interview-bootcamp-algorithms-and-data-structure/)

## トリボナッチ数列とは

>トリボナッチ数列は 0、0、1 で始まり、以後の項がその前の3つの項の和となる数列です。

引用：[フィボナッチ数列（９）：　トリボナッチ数列とテトラナッチ数列の計算](http://www.enjoy.ne.jp/~k-ichikawa/Fibonacci9.html)

## 確認用のテスト
少し冗長かもですが、こんな感じに書いてみました↓

```js:src/test.js
const tri = require("./index");

test("関数が定義されているか", () => {
  expect(typeof tri).toEqual("function");
});

test("n=1の時", () => {
  expect(tri(1)).toEqual(0);
});

test("n=2の時", () => {
  expect(tri(2)).toEqual(1);
});

test("n=3の時", () => {
  expect(tri(3)).toEqual(1);
});

test("n=4の時", () => {
  expect(tri(4)).toEqual(2);
});

test("n=5の時", () => {
  expect(tri(5)).toEqual(4);
});

test("n=50の時", () => {
  expect(tri(50)).toEqual(3122171529233);
});

```

## for文を使ったやり方

一番最初に思い付いたのはfor文を使った基本的なやり方でした。

```js:src/index.js
function tri(n) {
  const arr = [0, 0, 1];
  for (let i = 3; i <= n; i++) {
    arr[i] = arr[i - 3] + arr[i - 2] + arr[i - 1];
  }
  return arr[n];
}

module.exports = tri;
```

次のコマンドでテストを実行！

```zsh
jest src/test.js
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/91808cf2-4979-6ddb-e9fc-258f7e0d50bd.png)
うまくいきました！

## 再帰関数を使ったやり方

再起的なやり方を使って、コードを短くできました！

```js
function tri(n) {
  if (n < 3) return n === 2 ? 1 : 0;
  return tri(n - 3) + tri(n - 2) + tri(n - 1);
}

module.exports = tri;
```

しかし、このやり方だと、どんどん枝分かれしていって、nが50の時には何回も同じ呼び出しをテストがなかなか終わりません。
なので、以下のようにメモ化をしてみます。

```js
function memorize(fn) {
  const cache = [];
  return function (args) {
    if (cache[args]) return cache[args];
    const result = fn.call(this, args);
    cache[args] = result;
    return result;
  };
}

function slowTri(n) {
  if (n < 3) return n === 2 ? 1 : 0;
  return tri(n - 3) + tri(n - 2) + tri(n - 1);
}

const tri = memorize(slowTri);

module.exports = tri;
```

少し複雑で分かりにくいかもしれません。一応説明↓

* triの宣言のところから見ていくと、memorizeはfunction(args){...}を返すので、argsにはnが入る。
* memorizeの中の関数では、配列cacheにnの時の値があればそれを返して、なければfnの引数nにargsに入っていたnが入って呼び出せれます。その後に求められた値がcacheに格納されます。
* fnはmemorizeの引数なので、slowTriです。
* slowTriのなかでnが３以上なら、枝分かれしてtriが呼び出されます。枝分かれしていきnが３より小さくなるまで繰り返されます。


![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/6bb6aae5-1f4c-a717-cab9-c82fcf87c0ad.png)

テストがうまくいきました！

## 終わりに
ここまで読んでいただきありがとうございました！私の説明が下手で少し分かりにくかったらごめんなさい！
結果的にfor文のやり方より長くなってしまいましたが、素のJavaScriptを使って、関数のメモカを書いたの初めてなので勉強になりました。アルゴリズムの問題は人によって時方が結構違って、毎回何かしら学べるので楽しいです。
感想やご要望などお待ちしております！

