---
title: GitHub CopilotのTips（VSCode）
tags:
  - GitHub
  - AI
  - VSCode
private: false
updated_at: '2025-05-15T20:03:49+09:00'
id: 51a75c8d2bb4f27efc5c
organization_url_name: null
slide: false
ignorePublish: false
---
GitHub Copilot（VSCodeでの使用）で意外と知らないかもしれないTipsをまとめました！

:::note warn
2025/5/15時点の情報です🙇‍♂️
:::


## 1. 同じワークスペースでチャットを複数動かせる

Copilotの右上の3点リーダーをクリックすると、エディターか新しいウィンドウでチャットが開ける。これを複数回選択すると同時に複数のチャットUIが起動します

それぞれに指示を与えると各々動きます（特にそれぞれが遅くなったりはしてなさそう）

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/02689a15-f3ad-4ce9-b452-2d35b561986a.png)


以下は紹介用のシンプルな例です

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/264d57f2-5d49-48cf-b7fa-b12a2c8735f3.png)


## 2. チャットの整理

タスクや話題ごとにチャットを整理するとモデルのトークン枯渇や誤った解釈を軽減できます
不要なチャットを右上の❌で消したり、違う話題の際は+ボタンで新しいチャットを開始したりなど

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f36f3ee0-e52d-4cb8-bb1f-f58196cfa80d.png)

また、AskモードとAgentモードは同じチャットを利用できるので、Askモードで実装を相談→Agentモードで実装を進めてもらうみたいなこともできる

## 3. セマンティック検索

検索ビューには、Copilotによって検索テキストと意味的に関連する結果も表示されます

また、チャットコンテキストの `#searchResults`（検索結果の表示）を使うと、検索結果をCopilotの入力として利用できます

キーワードは分かってないけど内容で検索をしたい時とか便利ですね、あと検索ワードも提案してくれる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/a5601078-a15d-471f-81ba-371ebfa8d882.png)

それらしい記述が見つけられませんでしたが、自分はこの機能を利用するために、リモートインデックスを作成する必要がありました↓

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/275cb6d2-ee37-4066-ab03-ed26a5960d8d.png)

## 4. チャット応答時に音を鳴らす

`accessibility.signals.chatResponseReceived` のsoundをonにすると、チャット応答時に「フォ↑ン↓」みたいな音が鳴ります

Agentモードとかで終わった時に気付けるのはありがたいです

ただ、鳴る時と鳴らない時があって、よく分かってない🫠

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/c79331b2-3bdf-444a-bdc4-0e787b5f9056.png)

accessibilityの項目で他にも音が鳴るタイミングを設定できます

## 5. 一部モデル対応機能

### エージェントモードでの高速編集

一部モデルでエージェントモードでの編集がかなり速くなりました（余裕で体感できるレベル）

#### 対応モデル

- GPT 4.1
- o4-mini
- Claude 3.7 Sonnet
- Claude 3.5 Sonnet

### 画像入力

画像をコンテキストとして渡すことができます

モデルの性能によっては、見た目通りを実装してもらったりと便利かもしれません

#### 対応モデル

- Claude 3.5 Sonnet
- Claude 3.7 Sonnet
- Claude 3.7 Sonnet Thinking
- Gemini 2.0 Flash
- Gemini 2.5 Pro
- GPT-4.1
- GPT-4o
- o4-mini →ドキュメントにありませんでしたが対応してそう

#### 対応している例

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/0f413f3c-8385-4c6c-baaa-fab10bb45bc4.png)

##### 対応してない例

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/4d1d0d02-90b0-49ce-9a0e-b587ebe91537.png)

拡張機能（Copilot Vision）としてありましたが、そちらは廃止予定で、Github Copilot Chatの組み込まれているものに置き換えられるみたいです

https://github.com/microsoft/vscode-copilot-vision

## おわりに

読んでいただきありがとうございます！

少しでも参考になれば幸いです🙇‍♂️

## 参考
https://code.visualstudio.com/updates/v1_100#_faster-agent-mode-edits

https://docs.github.com/en/copilot/using-github-copilot/copilot-chat/asking-github-copilot-questions-in-your-ide#attaching-images-to-your-chat-prompt

https://code.visualstudio.com/docs/copilot/chat/copilot-chat#_vision-preview

https://code.visualstudio.com/updates/v1_98#_copilot-vision-preview
