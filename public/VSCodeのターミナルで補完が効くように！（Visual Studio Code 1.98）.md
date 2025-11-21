---
title: VSCodeのターミナルで補完が効くように！（Visual Studio Code 1.98）
tags:
  - Git
  - Terminal
  - VSCode
private: false
updated_at: '2025-03-11T15:34:43+09:00'
id: 45a0f2bcf0234d153f09
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
3/6にリリースされたVSCodeのバージョン1.98で、ターミナルでの補完機能がありました！

この記事ではターミナルの補完機能を使用例とともにざっくりお伝えできればと思いますー

# 導入
- VSCodeを最新バージョンにアップデート
- 設定から `terminal.integrated.suggest.enabled` を有効にする

# 使用例
## コマンドの補完
例）lを入力すると、lから始まるコマンドの候補が表示されます

![](https://storage.googleapis.com/zenn-user-upload/02b1451e84d9-20250307.png)

## ディレクトリ、ファイルの補完
例）lsの後に.や/を入力するとディレクトリ、ファイルの候補が表示されます

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/46ec0596-cb1d-4fca-89af-9bf9711652dd.png)

## オプションの補完
例）lsの後に-を入力すると、オプションの候補が表示されます

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/7eb251a0-f5ff-4ada-9d4b-57b68c0c1e49.png)


## gitのブランチの補完
例）gitコマンドでブランチの候補が表示されます

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/e45c1ae7-dd02-484b-ae8f-b13b7f48e106.png)

# その他
今回はターミナルの補完機能を取り上げましたが、GitHub Copilotの方でも色々アップデートされていました！

Copilot Chatではエラーやフォルダーもチャットに添付できるようになったり、#codebaseの検索機能が強化されたのも便利そうに感じました。

Agentモードはまだ使えてないので使っていきたいです！


# 終わりに
VSCodeのターミナルよく使うよって人は、ぜひVisual Studio Code 1.98 をダウンロードして、試してみてください〜

少しでも参考になったら嬉しいです！
