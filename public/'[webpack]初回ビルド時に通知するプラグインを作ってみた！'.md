---
title: '[webpack]初回ビルド時に通知するプラグインを作ってみた！'
tags:
  - JavaScript
  - webpack
private: false
updated_at: '2020-12-21T23:37:06+09:00'
id: 924f8b6c06a8253ddf39
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
webpackのビルド完了時に通知してくれるライブラリでwebpack-build-notifierなどがありますが、見た感じ初回ビルドのみはなかったので、今回はnode-notifierを使って、初回ビルド時に通知してくれるwebpackのプラグインを作ってみました！
webpackの簡単な設定などは済んでいる前提で進めていきます。

以下のサイトを参考にさせていただきました。
[node-notifier](https://github.com/mikaelbr/node-notifier)
[簡単な Webpack plugin を作成して Webpack と仲良くなる (ビルド時情報を console.log に表示する)](https://o296.com/e/%E7%B0%A1%E5%8D%98%E3%81%AAWebpackplugin%E3%82%92%E4%BD%9C%E6%88%90%E3%81%97%E3%81%A6Webpack%E3%81%A8%E4%BB%B2%E8%89%AF%E3%81%8F%E3%81%AA%E3%82%8B(%E3%83%93%E3%83%AB%E3%83%89%E6%99%82%E6%83%85%E5%A0%B1%E3%82%92console.log%E3%81%AB%E8%A1%A8%E7%A4%BA%E3%81%99%E3%82%8B).html)

## インストール
デスクトップ通知ができる、node-notifierをインストールします。

```zsh
## npm
npm install node-notifier

## yarn
yarn add node-notifier
```

## プラグインを作成
適当な名前でファイルを作ります。今回は`first-build-plugin.js`という名前にしました。

```js:first-build-plugin.js
const notifier = require('node-notifier');

module.exports = class FirstBuildPlugin {
  constructor() {
    this.is_build = false;
  }

  apply(compiler) {
    compiler.hooks.done.tap('FirstBuildPlugin', () => {
      if (!this.is_build) {
        notifier.notify({
          title: 'webpack',
          message: 'ビルドが完了しました！',
          sound: 'Ping',
        });
        this.is_build = true;
      }
    });
  }
};
```

- webpackでpluginを参照するときにapplyというのが実行されて、applyはcompilerを引数にとります。
- compilerからアクセスできるhooksで実行タイミングを設定することができ、doneはコンパイル完了時に呼び出されるhooksです。
- なので、その中にnode-notifierの通知の記述をしていますが、このままだとビルド時に毎回通知が来てしまうのでif文で初回のみになるようにしています。
- title,messageで通知のメッセージを、soundで通知オンを設定しています。他にもiconで画像を設定したりなどできます。詳しくはnode-notifierのgithubページを見てみてください。

## プラグインの読み込み

webpackの設定ファイルで読み込みます。

```js:webpack.config.js
// 〜　省略　〜　他のライブラリなどインポート
const FirstBuildPlugin = require('./first-build-plugin.js'); // ファイル名や場所によって変えてください

module.exports = {
// 〜　省略　〜　設定など
  plugins: [
    // 〜　省略　〜　他のプラグインなど
    new FirstBuildPlugin(),
  ]
};

// 〜　省略　〜
```

以下のコマンドを実行します。

```zsh
webpack --watch
```

<br/>
↓こんな感じで表示されます！
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/1e63af41-4b47-a4ac-8fb6-dedacbc93024.png)

## 終わりに
ここまで読んでいただきありがとうございます！この記事が少しでもお役に立てたら嬉しいです！
初回ビルドのみじゃなくて、毎回表示したりしたい場合は`webpack-build-notifier`ですぐ使えるのでオススメです。
webpack5が少し前にリリースされたので見ていきたいですね〜




