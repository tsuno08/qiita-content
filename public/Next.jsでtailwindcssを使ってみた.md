---
title: Next.jsでtailwindcssを使ってみた
tags:
  - CSS
  - JavaScript
  - React
  - Next.js
  - tailwindcss
private: false
updated_at: '2020-12-30T15:26:50+09:00'
id: 31fe34455717650127e8
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
Next.jsのcssにtailwindcssが使われているのを結構見かけたので使ってみました。Next.js特有の設定とかは特にしていないのでReactでも多分変わらないと思います。

参考：[tailwindcss](https://tailwindcss.com/)

## tailwindcssとは
迅速かつ簡単にデザインシステムを構築するためのユーティリティを提供するcssフレームワークです。
有名なものでbootstrapがありますが、個人的にbootstrapはスタイルの形がある程度スタイルが出来上がっているのに対し、tailwindcssは１から自由に作る印象です。

## 簡単な例
```tsx
<h1 className="bg-black text-red-500 p-5 m-5 text-center">hello world</h1>

/* 
background-color: #000000;
--tw-text-opacity: 1;
color: rgba(239, 68, 68, var(--tw-text-opacity));
padding: 1.25rem;
margin: 1.25rem;
text-align: center;
*/
```

↑こんな感じにクラスネームに記述していきます。コメントと同じスタイルが当たっています。

## 拡張機能
この拡張機能が入力候補やシンタックスハイライトが聞いてとても便利なので迷わず入れましょう！
[Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)

## 導入
`create-next-app`などでNext.jsのプロジェクトを作成したら、以下のコマンドでインストールします。

```zsh
## npm
npm install tailwindcss@latest postcss@latest autoprefixer@latest
## yarn
yarn add tailwindcss@latest postcss@latest autoprefixer@latest
```

インストールが完了したら、以下のコマンドで設定ファイルを作成します。

```zsh
npx tailwindcss init -p
```

すると、`postcss.config.js`、`tailwind.config.js`が作成されると思います。

その後、一番上に当たるファイル（`_app.tsx`など）にインポートするのですが、そのまま使う方法とカスタマイズする方法があるので紹介します。

### そのまま使う
まず、下の例がそのまま使う方法です。

```tsx:pages/_app.tsx
import React from "react";
import 'tailwindcss/tailwind.css';
import { AppProps } from "next/app";

export default function App({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
```

### カスタマイズする
そしてもう一つのカスタマイズする方法は別のCSSファイルを作って、インポートします。

```css:styles/globals.css
@tailwind base;
@tailwind components;
@tailwind utilities;

/* ベーススタイル設定 */
@layer base {
  html,body {
    @apply h-full;
  }
}

/* ユーティリティをまとめたコンポーネントスタイル作成 */
@layer components {
  .btn-blue {
    @apply px-4 py-2 font-bold text-white bg-blue-500 rounded;
  }
}

/* 独自のユーティリティ追加 */
@layer utilities {
  .bg-custom-black {
    background-color: #282c34;
  }
}

```

`base`でタグなどのベースのスタイル、`components`でひとまとめにした汎用スタイル、`utilities`で新しいユーティリティの作成ができ、そのまま使えます。レスポンシブや疑似要素のユーティリティを作成する場合は、`@variants`というのを使う必要があるみたいです。

```css
@layer utilities {
  @variants responsive {
    .bg-custom-black {
      background-color: #282c34;
    }
  }
  @variants responsive {
    .bg-custom-smoke {
      background-color: #f6f6f6;
    }
  }
}
```


このままだと`@apply`の後に赤線が出るので、vscodeの設定を書きます。

```json:.vscode/settings.json
{
  "css.validate": false
}
```

これでエラーが出なくなると思います。

作ったCSSファイルをインポートすれば使えるようになります。

```tsx:pages/_app.tsx
import React from "react";
import '../styles/globals.css'
import { AppProps } from "next/app";

export default function App({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
```

## 疑似要素
下のようにhover時に反映させたかったら、`hover:`に続けて書きます。activeだったら`active:`の後、focusだったら`focus:`の後みたいな感じです。

```tsx
<button class="bg-purple-600 hover:bg-purple-700">
    PUSH
</button>
```

## レスポンシブ
下の表のように範囲が分けられていて、疑似要素と同じような感じでxlサイズにしたいときは`xl:`に続けて記述する感じにします。

>|Breakpoint prefix|	Minimum width|	CSS|
|---|---|---|
sm|	640px|	@media (min-width: 640px) { ... }|
md|	768px|	@media (min-width: 768px) { ... }|
lg|	1024px|	@media (min-width: 1024px) { ... }|
xl|	1280px|	@media (min-width: 1280px) { ... }|
2xl|	1536px|	@media (min-width: 1536px) { ... }|
ドキュメントより引用

```tsx
<h1 className="bg-black xl:bg-white">hello world</h1>
```



## 終わりに
ここまで読んでいただきありがとうございます、少しでも参考になったら嬉しいです！
具体的なスタイルの説明に関しては量がとても多く、[ドキュメント](https://tailwindcss.com/)がわかりやすいので興味があったら見てみてください。他にも設定ファイルで色々カスタマイズできると思います。
使い始めたばかりでまだ少し時間がかかってしまいますが、慣れるととても書きやすそうです！

