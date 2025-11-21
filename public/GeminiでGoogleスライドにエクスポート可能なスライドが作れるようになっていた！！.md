---
title: GeminiでGoogleスライドにエクスポート可能なスライドが作れるようになっていた！！
tags:
  - AI
  - Googleスライド
  - Gemini
  - LLM
private: false
updated_at: '2025-10-27T12:10:56+09:00'
id: 198fceb42a261a3914e0
organization_url_name: null
slide: false
ignorePublish: false
---
:::note warn
技術的な内容は少ないです🙇‍♂️
エンジニアでもLTなどでスライドを作る機会が多いので役立ちそうかなと思って書いてみました！
:::

## はじめに

GeminiのCanvas機能で、Googleスライドにエクスポート可能なスライドが作れるようになっていました！

10月のGemini Dropsで紹介されていて気付きました。Proプランからリリースされているみたいです。
**※Proプランでもまだ使えない方もいるようで、おそらく展開中？🤔**

> [日本語訳] あらゆるソースをアップロードして、画像やデータビジュアライゼーションを盛り込んだプレゼンテーションを作成できます。Googleスライドにエクスポートして仕上げを加えることもできます。Pro版は本日から、無料版は今後数週間以内にリリース予定です。

https://x.com/GeminiApp/status/1981760428473757808

https://blog.google/products/gemini/gemini-drop-october-2025/

## 使ってみた

### 簡単な例

Canvas側は以下のような表示になります↓

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/b0a89658-fd79-4183-91d9-e165b60b0cb7.png)

「スライドにエクスポート」を押すと、Googleスライド側にエクスポートされます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/115e480a-7587-4f52-a4d9-45055daebf15.png)

ちょっと複雑な図は画像として出力されていました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/b9fdfa80-1cb3-42b0-9414-a1e6885286ec.png)

文字は上から重ねているようで、簡単に編集できそうです！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/62ec9d9f-ce33-4077-8c11-d5e345a391f9.png)

### Qiitaの記事で試した例
最近書いた以下の記事をMarkdownファイルにして、スライドを作ってもらいました！

https://qiita.com/Nozomuts/items/430261fa996cba5fd466

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f1880a37-df7f-4be2-9afa-ed1cef8005d8.png)

全体が見られるので、Googleスライドにエクスポートした写真を添付します。
一部表示崩れはあるものの、内容には忠実そうですねー

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/5f934ee9-1c32-459d-bf63-4805c93d88c2.png)

「白背景で図解多め〜」みたいな感じで試したバージョンです。
こちらの方は目立った表示崩れがなくて良さげですね！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/ecf03ea4-eee5-404c-a4df-78b79bf65e6f.png)


## おわりに
読んでいただきありがとうございます🙇‍♂️
業務で使える良い感じのスライド生成があまりなかったので嬉しいです！
しかもGoogleスライド側で編集できるので、スライドの叩き台としてはかなり便利そうです
