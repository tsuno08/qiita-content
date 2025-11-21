---
title: Googleのコーディングエージェント「Jules」を早速使ってみた！
tags:
  - GitHub
  - Google
  - AI
private: false
updated_at: '2025-05-20T13:25:05+09:00'
id: 20ec1438cf478932201b
organization_url_name: null
slide: false
ignorePublish: false
---
この記事は、Googleから発表されたコーディングエージェント「Jules」を試した記録です。
結構前から発表はされてましたが、ついに来ましたね！
最近出たOpenAIのCodexに近いのかなと思ってます（実際には使えてないですが…）

なんと今のところ無料で使えます！！

# Julesとは？
（日本語訳）Jules は、バグ修正、ドキュメント追加、アプリのアップデート、新機能の実装を支援するソフトウェアコーディングエージェントです。GitHub と統合されており、非同期で動作します。つまり、タスクを送信した後、別の作業を行い、完了したら再開することができます。Jules は現在パブリックベータ版です。
> Jules is a software coding agent that helps you fix bugs, add documentation, update your app, and implement new features. It integrates with GitHub and works asynchronously — meaning you can submit a task, go do something else, and return when it’s done. Jules is currently in Public Beta.

https://jules.google/docs

Jules は仮想マシンで実行され、リポジトリのクローンを作成し、依存関係をインストールし、プロンプトに基づいて変更を加えるような動きをするみたいです。

## 画面

画面は以下のような感じになってます！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/16b45f8c-1015-4792-b492-31401cc28dd0.png)

GitHubに接続するとチャット部分が以下の表示に切り替わります
左上でリポジトリ選択、右上でブランチを選択できます

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/7fd3efd9-f39c-41bd-a6d2-14189741799e.png)


ちなみにスマホからも使えそうでした！！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/c27c5279-634b-4c8d-9274-035a2d96092e.png)


# 利用方法
- Googleアカウントがあれば https://jules.google.com で使用できます
- waitリストで待つ状態になり、すぐには使えないかもしれません

# 試す
## タスクの実行をしてみる
お願いしたいタスク内容を入力して送信すると、まずは英語で実装計画が返って来ました
日本語にするようにお願いしたらしてくれました！
（タスク内容などは重要ではないので割愛）

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/43234317-69d0-4f00-b2dc-b516dc01bbee.png)

数分で以下のように実装してくれました！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/29ccbf66-9587-4acd-a2c5-aa310250c9d3.png)

問題ないことを伝えると、PRのベースを作ってくれました

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/0de7aa79-66d1-41ca-b41b-6f9b63ae0310.png)

「Publish branch」を押して、GitHubで見るとこんな感じです！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f7245b66-4d09-4715-88e6-f39f20b5405c.png)

かなりシンプルな内容にはなりますが、UIも分かりやすいし、割とサクサク動いて良い感じでした！
通知の設定をできるのも助かりますねー

ちなみに当たり前ではありますが、プロンプトが具体的かつスコープが限定されているときに最も効果を発揮すると記載がありました👀

https://jules.google/docs/running-tasks/#write-a-clear-prompt

## コードキャストを試してみる
コードキャストというコードに関する音声概要？を生成する機能もあったので試します
左サイドバーでCodebasesを選択すると表示される画面の右側にあります

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/67ca446d-348d-4871-80d6-8b6c63a201ce.png)

「Generate today's」をクリックすると数分でできたのですが、英語でした😭
再生位置の指定やダウンロードも無いのでちょっと不便かなという感じです

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/47afbd6f-6b5d-4bec-9df8-1e77cec986eb.png)


# 制限事項
以下の制限事項があったので記載しておきます🙇
- タスクの同時実行数: 3
- 1日あたりのタスク: 5
- 1日あたりのコードキャスト: 5

一応上限を上げるリクエストフォームもありました👀

https://jules.google/docs/usage-limits/#requesting-higher-limits

# おわりに
ここまで読んでくださりありがとうございます！
実験的かつ制限はありますが、これが無料で使えるのがとてもありがたいですね
Codex使っている人の比較とかも見てみたいです
