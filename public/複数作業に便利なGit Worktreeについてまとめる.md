---
title: 複数作業に便利なGit Worktreeについてまとめる
tags:
  - Git
  - copilot
private: false
updated_at: '2025-10-29T16:11:26+09:00'
id: 04e9e7a6a7bea8c36cdd
organization_url_name: null
slide: false
ignorePublish: false
---
最近はコーディングエージェントを用いた並列開発でもよく聞くようになった、Git Worktreeについてまとめてみました！

:::note warn
この記事は一部AIを使って書いています
最新の情報は [公式ドキュメント](https://modelcontextprotocol.io/docs/getting-started/intro) などをご確認ください
:::

## はじめに

開発中にこんな「つらみ」を感じたことはありませんか？

- 機能開発中に緊急の修正依頼が来て、とりあえず `git stash` したものの、後でどれだったか忘れてしまう
- `git stash pop` したらコンフリクト発生
- プルリクエストのレビューや並行開発のために、同じリポジトリを `git clone` して複数用意しているが、ディスク容量がもったいないし、クローン間の同期も面倒

これらの課題は、`git worktree` を使うことで解決できるかもしれません！

## git worktree とは？

`git worktree` は、Git 2.5 (2015 年リリース) から追加された機能です。

一言でいうと、1 つのリポジトリ（`.git`ディレクトリ）を共有したまま、複数の作業ディレクトリ（ワークツリー）を作成できる機能です。

### どんなメリットがある？

`git worktree` には、主に以下のようなメリットがあります。

- `stash` の代わり
  - 現在の作業を `stash` することなく、別のブランチ用のワークツリーをサッと作成し、緊急のバグ修正やレビュー作業に素早く移ることができます
- 環境が混ざらない
  - ブランチごとに独立したディレクトリを持つため、ファイルの状態はもちろん、`node_modules` のような依存関係が混ざることはありません
- ディスク容量の節約
  - `.git` ディレクトリは共有されるため、リポジトリを丸ごと `clone` し直すよりもディスク容量を節約できます


## 基本的なコマンド

`git worktree` にはいくつかコマンドがありますが、まずは以下の 3 つを覚えておけば十分かと思います。

- `git worktree add <path> [<branch>]`: 新しいワークツリーを `<path>` に作成
- `git worktree list`: 現在のワークツリーの一覧を表示
- `git worktree remove <path>`: 指定したワークツリーを削除


## 具体的な使用例

いくつか具体的な使用例を書いてみました！

### 機能開発中の緊急バグ修正

`feature-A` ブランチで開発中に、本番環境の緊急バグ修正を依頼されたケースです。

`stash` することなく、`feature-A` の作業を中断したまま、安全に修正作業へ移ることができます。

```bash
# 現在のディレクトリ: ~/my-project (feature-A ブランチ)

# 1. mainブランチをベースに、hotfix/bug-123ブランチを
#    ../hotfix-123 ディレクトリに作成
git worktree add -b hotfix/bug-123 ../hotfix-123 main


# 2. 新しいワークツリーに移動して修正作業
cd ../hotfix-123

# ...ここでコードを修正し、コミット、プッシュする

# 3. 修正が終わったら、元の作業ディレクトリに戻る
cd ~/my-project


# 4. 修正がマージされたら、不要になったワークツリーを削除
git worktree remove ../hotfix-123

```

### プルリクエストのレビュー

同僚からプルリクエストのレビューを依頼され、手元で動かして確認したいケースです。

```bash
# 1. レビュー対象のブランチ (origin/feature/colleague-B) を
#    ../pr-review-B ディレクトリに作成
git worktree add ../pr-review-B origin/feature/colleague-B


# 2. レビュー用のディレクトリに移動し、動作確認
cd ../pr-review-B

# npm install やビルド、テストなどを実行する 

# 3. レビュー完了後、ワークツリーを削除
git worktree remove ../pr-review-B

```

###  AI との並行開発

これは少し発展的な使い方かもしれませんが、自分が新機能の実装に集中しつつ、軽微な修正やリファクタリングを AI エージェントに任せる、といった並行開発にも応用できます。

```bash
# AIに軽微な修正を任せるためのワークツリーを作成
git worktree add -b feature/ai-fix ../ai-fix develop


# AIにリファクタリングを任せるためのワークツリーを作成
git worktree add -b feature/ai-refactoring ../ai-refactor develop


# 自分: メインのディレクトリでコア機能の実装に集中 
# AIエージェント1: ../ai-fix ディレクトリで軽微な修正を実行 
# AIエージェント2: ../ai-refactor ディレクトリでリファクタリングを実行 
```


## VSCode から使う

コマンドを覚えるのが大変...という方には、GUI ツールを使うのが簡単です。
特に VSCode は、バージョン 1.103 から `git worktree` のサポートが強化されています。

ソース管理タブのメニュー（`...`）から「ワークツリー」を選ぶだけで、新しいワークツリーの作成、一覧、削除が直感的に行えます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/a031ecac-fad7-4bfb-9ef8-3f96a33606b2.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/fbdfec4b-492c-4f6a-a33f-eb914528c940.png)


また、コマンドパレット ( `Ctrl+Shift+P` または `Cmd+Shift+P` ) で `>worktree` と入力しても、関連コマンドに素早くアクセスできます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/6471cb5c-292f-4415-91bb-ba3b3fe3c732.png)

キーバインドに追加するのも良さそうです！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/e04386e8-a4cd-403b-8490-64d68ce07585.png)

## CLI で使いたい場合の工夫

CLI での操作に慣れている方は、`~/.gitconfig` にエイリアス（ショートカット）を設定しておくと便利です。

このように設定しておけば、`git wtl` のような短いコマンドで実行できるようになります。


```ini: ~/.gitconfig
[alias]
  wta = worktree add -b

  wtl = worktree list

  wtr = worktree remove

  wtp = worktree prune

```


また、GitHub Copilot のようなコーディングエージェントと `git worktree` を組み合わせる、という方法もあります。 

例えば、以下のようなカスタムプロンプトを用意しておくことで、ワークツリーの作成からエディタの起動までをコーディングエージェントにお任せできます

````md:worktree.prompt.md
---
mode: 'agent'
tools: ['usages', 'think', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo', 'todos', 'createFile', 'createDirectory', 'editFiles', 'runCommands', 'search']
description: 'ユーザーの指示をもとに、git worktree を作成し、VS Code で開く。'
---
# Git worktree 自動作成 & VS Code オープン プロンプト

## 役割
あなたは Git に精通したシェル実行アシスタントです。
ユーザーの入力（用途/目的やレビュー対象）から安全なブランチ名を自動生成し、`git worktree` を用いて新しい作業ディレクトリを作成し、最後に VS Code で開きます。

## ルール
- `git worktree` を使用すること
- 作成先ディレクトリは `../<repository>.worktrees/<branch>`（基準はリポジトリルート）
- ブランチ名は用途に応じた **kebab-case** で **20 文字以内** を厳守（例：「hogeのバグ修正用」→ `hotfix-hoge-bugfix`、「ログイン機能追加」→ `feature-login`、「レビュー: feature/search-v2」→ `review-feature-search-v2`）
- レビュー用途で、`origin/<branch>` が存在する場合はそれを指定して作成すること
- 最初に `git fetch origin --prune` を実行し最新状態に同期すること

## 手順
### 1. **コンテキスト検出**
```bash
# リポジトリルート確認。
git rev-parse --show-toplevel

# 前準備として最新の origin 情報を取得。
git fetch origin --prune
```
### 2. **用途/要約抽出 → ブランチ名生成**（ルール参照）
- レビュー用語（「レビュー」「review」「PR#」など）を検出したら **review モード**、それ以外は **new モード** とする。
### 3. **作成先の決定**
- `../<repository>.worktrees/<branch>` をパスに採用。
- パス衝突時は `-2`, `-3`…の連番で回避するか、中止して理由を報告。
### 4. **分岐**
**review モード**：`origin/<branch>` が存在する場合はそれを指定。
```bash
git worktree add ../<repository>.worktrees/<branch> origin/<branch>
```
**new モード**：存在しなければ新規ブランチ。ベースは指定がなければ `origin/main`。
```bash
git worktree add ../<repository>.worktrees/<branch> -b <branch> <base>
```
### 5. **VS Code を開く**
```bash
open -a "Visual Studio Code" ../<repository>.worktrees/<branch>`
```

````

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/2d946bb8-e1ba-45e1-a793-6f8e51000c48.png)


## git worktree の注意点

便利な `git worktree` ですが、いくつか注意点もあります。

- 同じブランチはチェックアウトしない
  - 複数のワークツリーで、同時に同じブランチをチェックアウトすることは避けるべきです。予期せぬ状態になる可能性があります
- 依存ライブラリは別途必要
  - `node_modules` やサブモジュールなどは、ワークツリーごとに（`cd` して）インストールや更新が必要です
- IDE のサポート
  - IDE によっては、ワークツリーの仕組みをうまく認識できない場合があります
  - その際は、各ワークツリーのディレクトリを別ウィンドウで開くのが確実です
- 削除はコマンドで行う
  - ワークツリーのディレクトリを `rm -rf` などで手動削除すると、Git の管理情報に残骸が残ってしまいます。必ず `git worktree remove <path>` を使うことをお勧めします
  - もし手動で消してしまった場合は、`git worktree prune` を実行すると、残骸を掃除できます

## 最後に
ここまで読んでいただき、ありがとうございました！

`git worktree` は、`stash` や `clone` の「つらみ」を解消し、開発フローをスムーズにしてくれる非常に便利な機能です。

同じリポジトリで複数の作業場所が欲しいと感じた時は、ぜひ試してみてください！

