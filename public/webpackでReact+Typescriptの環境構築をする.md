---
title: webpackでReact+Typescriptの環境構築をする
tags:
  - JavaScript
  - 環境構築
  - TypeScript
  - webpack
  - React
private: false
updated_at: '2020-09-24T23:58:02+09:00'
id: 30b3a4e35c6834e614f8
organization_url_name: null
slide: false
ignorePublish: false
---
Qiita初投稿です。これからどんどん書いていこうとおもうのでご意見・ご要望ありましたら気軽にコメントしていただけると、自身の成長にもつながるのでよろしくお願いします！



## webpackとは？
一言で表すとJavascriptを1つのファイルにまとめる事ができるツールです。
他にも様々な機能がありますが、ここでは割愛します。

### 1つのファイルにまとめるメリット
1つにまとめることでブラウザとサーバーの通信回数を減り、通信速度が速くなります。

## 実際にやってみる

実際にwebpackを使って、React+Typescriptの環境構築をしてみましょう。

### ディレクトリの作成

まず、ターミナルで以下のコマンドでディレクトリを作り、VSCodeで開きます（ファイル名の部分は好きな名前で大丈夫です）。codeコマンドがない場合は普通にファイル作って、VSCodeで開けば大丈夫です。

```:ターミナル
mkdir [ファイル名]

code [ファイル名]
```

何もファイルがない状態だと思います。

### 必要なパッケージのインストール

次にVSCodeのターミナルで以下のコマンドを打つと、`package.json`が作成されます。

```:VSCodeのターミナル
//npmを使う場合
npm init -y

//yarnを使う場合
yarn init -y
```

その後必要なパッケージをダウンロードしていきます。

```:VSCodeのターミナル
//npm
npm install --save react react-dom

npm install --save-dev @types/react-dom @types/webpack @types/webpack-dev-server ts-loader ts-node typescript webpack webpack-cli webpack-dev-server

//yarn
yarn add react react-dom

yarn add -D @types/react-dom @types/webpack @types/webpack-dev-server ts-loader ts-node typescript webpack webpack-cli webpack-dev-server
```

インストールしたパッケージの簡単な説明を表にまとめました。

|||
|:---:|:---|
|react,react-dom| Reactを書くのに必要|
|@types/~|@typesに続くパッケージの型宣言ファイルが含まれている|
|ts-loader|TypescriptをJavascriptにコンパイルするのに使う|
|ts-node|Typescriptのファイルを直接実行できる|
|typescript|Typescriptを書くのに必要|
|webpack|Javascriptのファイルを1つにまとめる|
|webpack-cli|webpackコマンドを使うのに必要|
|webpack-dev-server|ファイルを変えた時に差分ビルドをしてくれる|

### webpackの設定


次に以下のコマンドを実行して、webpackの設定ファイルである、`webpack.config.ts`を作成します。

```:VSCodeのターミナル
touch webpack.config.ts
```

作成された`webpack.config.ts`に以下のように記述します。`webpack.config.js`でも大丈夫です。その場合はConfigurationの部分を消します。

設定ファイルは勉強も兼ねて、デフォルトと同じになっているところも書いているため、少し冗長な部分があります。

```ts:webpack.config.ts
import path from 'path';
import { Configuration } from 'webpack';

const config: Configuration = {
    context: path.join(__dirname, 'src'),
    entry: './index.tsx',
    output: {
        path: path.join(__dirname, 'dist'),
        filename: 'bundle.js',
        publicPath: '/assets',
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: 'ts-loader',
            },
        ],
    },
    mode: "development",
    resolve: {
        extensions: ['.ts', '.tsx', '.js', '.jsx'],
    },
    devtool: "inline-source-map",
    devServer: {
        contentBase: path.join(__dirname, 'static'),
        open: true,
        port: 3000,
    },
};

export default config;
```

webpack.config.ts(js)の設定ファイルの簡単な説明を以下の表にまとめました。

|||
|:---:|:---|
|path|ファイルやディレクトリのpathを操作できる。デフォルトでnode_modulesに入っている|
|Configuration|configのtype|
|__dirname|カレントディレクトリを示す|
|output|ファイルの出力設定(path:出力ファイルのディレクトリ名、filename:出力ファイル名、publicPath:バンドルファイルをアップロードする場所)|
|module|rules(test:コンパイルするファイル、use:コンパイルに使うツール)|
|mode|開発(development)か本番(production)か|
|resolve|extensions:importで省略したい拡張子|
|devtool|デバッグ用のツール(mode:develop)|
|devServer|開発用のサーバー(contentBase:サーバーの起点とするディレクトリ、open:ブラウザを自動で起動するか、port:ポート番号)|

### Typescriptの設定

次に以下のコマンドを実行して、Typescriptの設定ファイルである、`tsconfig.json`を作成します。

```:VSCodeのターミナル
touch tsconfig.json
```

作成された`tsconfig.json`に以下のように記述します。

```json:tsconfig.json
{
    "compilerOptions": {
        "sourceMap": true,
        "baseUrl": "./",
        "target": "es5",
        "strict": true,
        "module": "commonJs",
        "jsx": "react",
        "lib": ["ES5", "ES6", "DOM"],
        "allowSyntheticDefaultImports": true,
        "esModuleInterop": true,
        "isolatedModules": true,
    }
}
```

tsconfig.jsonの設定の簡単な説明を以下の表にまとめました。

|||
|:---:|:---|
|sourceMap|ソースマップを見れるようにするか|
|baseUrl|tsconfig.jsonの場所|
|target|どのバージョンでJavascriptを出力するか|
|strict|型付けのルールを厳しくする|
|module|Typescriptのモジュールをどのバージョンで出力するか|
|jsx|jsxの書式を有効化|
|lib|コンパイルに使用する組み込みライブラリ|
|allowSyntheticDefaultImports|default importを使うか|
|esModuleInterop|import *  以外も使えるようにするか|
|isolatedModules|exportを必須にするか|


次に以下のコマンドでsrcディレクトリを作り、その中に`index.tsx`を作成します。

```:VSCodeのターミナル
mkdir src && touch src/index.tsx
```

作成した`src/index.tsx`に以下のように記述します。

```tsx
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<h1>Hello World!</h1>, document.getElementById('app'));
```

次に以下のコマンドでstaticディレクトリを作成し、その中に`index.html`を作成します。

```:VSCodeのターミナル
mkdir static && touch static/index.html
```

作成した`static/index.html`に以下のように記述します。

```html
<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
    </head>
    <body>
        <div id="app"></div>
        <script src="/assets/bundle.js"></script>
    </body>
</html>
```

###　実行

これで最低限の環境は整いました。
VSCodeのターミナルで以下のコマンドを実行します。

```:VSCodeのターミナル
//npm
npx webpack-dev-server

//yarn
yarn webpack-dev-server
```

すると`localhost:3000`がブラウザで開かれて、Hello World!が表示されると思います。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/62354196-037c-40ce-ad9f-0916ff3a43d0.png)


ここまで読んでいただきありがとうございます。少しでもwebpackについてイメージできたら嬉しいです。
今後も記事を書いていこうと思うので感想などいただけるとモチベーションにつながります。

## 参考記事

* [最新版で学ぶwebpack4入門 JavaScriptのモジュールバンドラ](https://ics.media/entry/12140/)
* [tsconfig.jsonの全オプションを理解する（随時追加中）](https://qiita.com/ryokkkke/items/390647a7c26933940470#baseurl)
* [Typescriptリファレンス](https://www.typescriptlang.org/tsconfig#baseUrl)



