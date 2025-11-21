---
title: Chrome拡張機能の実装（入門から公開まで）
tags:
  - HTML
  - CSS
  - JavaScript
  - Chrome
  - extension
private: false
updated_at: '2023-08-23T00:33:19+09:00'
id: fc1d4f8fc995d830817d
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
ふと、Chrome拡張機能を作ってみたいなと思って調べて作ってみました。
まずは用語とかの説明をつらつらと書いていきます。

作ってるところだけ見たいよって方は「作る」まで飛ばして読んでみてください！

# 拡張機能の開発について
## Chrome拡張機能とは
Chromeブラウザに機能を追加することができます。
自分がよく使うものだと [DeepL翻訳](https://chrome.google.com/webstore/detail/deepl-translate-reading-w/cofdbpoegempjloogbagkncekinflcnj) や [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi) とかがあります。

## 拡張機能で使う技術
主に HTML、CSS、JavaScript で作られます。

### Chrome API
拡張機能では Chrome API にアクセスすることができます。
これはウェブサイトの機能や動作を変更したり、DevTools に機能を追加したりできます。

## 拡張機能で使われるファイル
### manifest.json
拡張機能の設定ファイルのようなものです。
- 拡張機能の名前・説明・バージョン・アイコン
- Chrome API を使うためのキー
- 与える権限
- 各ファイルのある場所

などを設定できます。

唯一の必須ファイルであり、ルートディレクトリに配置します。

公式の `manifest.json` の例はこちらにあります↓

https://developer.chrome.com/docs/extensions/mv3/manifest/#manifest-examples

※manifest_version は2が廃止予定のため、3を使うようにしましょう。

https://developer.chrome.com/docs/extensions/migrating/mv2-sunset/

### サービスワーカー
拡張機能でのサービスワーカーはイベントハンドラ（あるイベントに対応して処理を実行する）の役割を持ちます。

Web のサービスワーカーとは異なり、新しいページに移動したり、通知をクリックしたり、タブを閉じたりといったイベントにも対応しています。

全ての Chrome API にアクセスするとができますが、DOM にアクセスすることはできません。
DOM へのアクセスはコンテンツスクリプトが行います。

サービスワーカーを登録するには　`manifest.json` の `background > service_worker` の箇所に単一のjsファイルを指定します。
```json:manifest.json
{
  "name": "My extension",
  ...
  "background": {
    "service_worker": "service-worker.js"
  },
  ...
}
```

### コンテンツスクリプト
拡張機能はコンテンツスクリプトを使って、Webページにコードを注入します。
コンテンツスクリプトは開いているページのDOMツリーにアクセスできるため、拡張機能はページに新しい要素を挿入したり、ページを変更したりできます。

また、限られた Chrome API にアクセスできます。
アクセスできない Chrome API はサービスワーカー経由でアクセスすることができます。

コンテンツスクリプトを登録するには `manifest.json` の `content_scripts` の箇所にファイルを指定します。
`matches` はコンテンツスクリプトを実行するページを指定します（必須）。
```json:manifest.json
{
 "name": "My extension",
 ...
 "content_scripts": [
   {
     "matches": ["https://*.nytimes.com/*"],
     "css": ["my-styles.css"],
     "js": ["content-script.js"]
   }
 ],
 ...
}
```

### ユーザーインターフェースのカスタマイズ
HTML、CSS、JavaScript を用いて、Chromeブラウザにもともとあるユーザーインターフェースをカスタマイズすることができます。一例を紹介します。

#### ポップアップ
HTMLを用いて、ユーザーが拡張機能のアイコンをクリックしたときに特別なウィンドウを表示することができます。
Webページと同じように動作するため、styleタグやscriptタグにリンクを含めることができますが、インラインはダメみたいです。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/aff7eeb0-8e65-b282-d029-ba1c15df2a44.png)

ポップアップを登録するには、`manifest.json` の `action > default_popup` にHTMLファイルを指定します。
```json:manifest.json
{
  "name": "My extension",
  ...
  "action": {
    "default_popup": "popup.html"
  }
  ...
}
```

#### ページの上書き
Google Chrome が通常提供するページ（新規タブページ、ブックマークページ、履歴ページ）を、拡張機能のHTMLファイルで置き換えることができます。
1つの拡張機能につき、1つのページしか上書きできません。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/99aa1710-faed-a89c-9c43-dc165f79e13a.png)

ページの上書きを登録するには、`manifest.json` の `chrome_url_overrides` で `bookmarks`・`history`・`newtab` のいずれかのキーにHTMLファイルを指定します。
```json:manifest.json
{
  "manifest_version": 3,
  "name": "My extension",
  ...

  "chrome_url_overrides" : {
    "newtab": "myPage.html"
  },
  ...
}
```

#### 拡張機能の設定ページ
拡張機能を使うユーザーが機能の設定をできるページを用意することもできます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/46f2e839-27fe-fb7b-a67c-4e20cc4ab1db.png)

設定用のページを登録するには、`manifest.json` の `options_page` にHTMLファイルを指定します。
```json:manifest.json
{
  "name": "My extension",
  ...
  "options_page": "options.html",
  ...
}
```

他にも色々なものがあるので、興味のある方は見てみてください！
https://developer.chrome.com/docs/extensions/mv3/devguide/

## ディレクトリ構成の例
ディレクトリ構成の例を載っけときます。
もちろん `manifest.json` の設定で決まるので、ある程度自分の好きなように作ってみてください！
```
my-extension/
├── manifest.json （必須）
├── service-worker.js
├── scripts/
│   └── content-script.js
├── popup/
│   ├── popup.html
│   ├── popup.js
│   └── popup.css
├── options/
│   ├── options.html
│   ├── options.js
│   └── options.css
├── icons/
│   ├── icon16.png
│   ├── icon48.png
│   └── icon128.png
└── ...
```


# 作る
今回は新しいタブを開いた時に大喜利が表示されるという、めちゃくちゃシンプルな拡張機能を作ってみました！
紹介したもの中だと「ページの上書き」に該当しますね！


まず、適当にディレクトリを作ります。
そのルートディレクトリで `manifest.json` を作って記述します。
```json:manifest.json
{
  "manifest_version": 3,
  "name": "フラッシュ大喜利: Flash Japanese Ogiri",
  "description": "「フラッシュ大喜利」は、新しいタブを開くたびに、瞬時に楽しさが広がる拡張機能です。\nFlash Japanese Ogiri is an extension that instantly adds to the fun every time you open a new tab. ",
  "version": "1.0",
  "icons": {
    "16": "images/icon-16.png",
    "32": "images/icon-32.png",
    "48": "images/icon-48.png",
    "128": "images/icon-128.png"
  },
  "chrome_url_overrides": {
    "newtab": "overrides/newtab.html"
  }
}
```

アイコンはフリーのものを見つけて、以下のサイトでリサイズしました。

https://resizeappicon.com/

HTML、CSS、JavaScript をそれぞれ書きます。
解説するほどのものでもないので、気になったらみて見てください↓

<details><summary>overrides/newtab.html</summary>

```html:overrides/newtab.html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>フラッシュ大喜利</title>
    <link rel="stylesheet" href="newtab.css" />
  </head>

  <body>
    <div class="odai-wrapper">
      <p id="odai-text">読み込み中...</p>
    </div>
    <script src="newtab.js"></script>
  </body>
</html>

```
</details>
<details><summary>overrides/newtab.css</summary>

```css:overrides/newtab.css
body {
  font-family: "Arial", sans-serif;
  margin: 0;
  height: 100vh;
  background: #f4f4f4;
  display: flex;
  justify-content: center;
  align-items: center;
}

.odai-wrapper {
  background: #ffffff;
  padding: 40px;
  border-radius: 5px;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.2);
}

#odai-text {
  font-size: 24px;
  text-align: center;
}

```

</details>
<details><summary>overrides/newtab.js</summary>

```js:overrides/newtab.js
const odaiArray = [
  "宇宙人が地球を訪れた時に最初に言った一言は？",
  "進化の結果、猫が新しく手に入れた能力とは？",
  "インターネットが突如消えた後、新しく流行った人間のコミュニケーション方法は？",
  "ペンギンが南極を出て最初に旅行した先はどこ？",
  "未来のオリンピックで追加される新種目とは？",
  "ロボットが人間の仕事を全て奪った後、彼らが楽しむ新しい趣味とは？",
  "世界中のチョコレートが突然変わってしまった！何に変わった？",
  "突如、全ての動物がしゃべれるようになった。犬が最初に飼い主に頼んだことは？",
  "未来のスマホの意外な新機能とは？",
  "タイムマシンで過去に行ったら、恐竜が人々と一緒に何をしていた？",
];

const odaiContainer = document.getElementById("odai-container");
const odaiText = document.getElementById("odai-text");

// ランダムな大喜利を取得
function getRandomOdai() {
  const randomIndex = Math.floor(Math.random() * odaiArray.length);
  return odaiArray[randomIndex];
}

// ランダムな大喜利を表示
function displayRandomOdai() {
  const randomOdai = getRandomOdai();
  odaiText.textContent = randomOdai;
}

// ページが読み込まれたときにランダムな大喜利を表示
displayRandomOdai();


```

</details>

はい、これで開発完了です！
めっちゃ簡単

# 動作確認
chrome://extensions にアクセスして、デベロッパーモードをオンにします。
オンにしたら「パッケージ化されていない拡張機能を読み込む」というボタンがあるので、クリックして今回作ったディレクトリを選択します

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/694a13e6-8b9d-1172-2f1b-a8f57e7d3aa4.png)

するとこんな感じで追加されました！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/cf5a28b7-1e3b-7e44-1f78-2b68c31744e5.png)

これで拡張機能が確認できるので、新規タブを開いてみます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/c44e9177-9f93-067e-a411-cbae94eba977.png)

表示されました！！！
これで動作確認はOKです。

# Chromeウェブストアへ
まず、Chromeウェブストアのデベロッパーアカウントが必要なので、作成します。
<b>※$5 かかります</b>

https://chrome.google.com/webstore/devconsole

登録できたら、アイテムタブで「新しいアイテム」ボタンをクリックします

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8a52bb60-339b-0297-fdac-40af2fbacf45.png)

するとモーダルが出てくるので圧縮したファイルを選択します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/60970742-9aaa-46e0-0907-2bd508fddff6.png)

そのあと色々入力を求められるので、入力できたら、「審査のため送信」をクリックすると送信されます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f3b90909-744c-386a-6082-46ce93c0ff91.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/39255632-caf9-e55c-d952-5de05d4a653a.png)

あとは審査が通ったら公開されます！！
（手抜きすぎるので限定公開で出しました）

# おわりに
最後まで読んでいただきありがとうございます！
今回書いたのは一部に過ぎないので、より深く知りたい方はドキュメントを見てみてください！
これを読んで便利な拡張機能を作ったりしてくれたら嬉しいです。感想などお待ちしております！

サンプルがとても充実してるので載せときます↓

https://github.com/GoogleChrome/chrome-extensions-samples

# 参考
https://developer.chrome.com/docs/extensions/
