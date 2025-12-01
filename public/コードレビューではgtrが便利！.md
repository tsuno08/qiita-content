---
title: コードレビューではgtrが便利！
tags:
  - Git
  - GitHub
private: false
updated_at: '2025-12-01T10:20:10+09:00'
id: 0f16c7de2a85a7aad6c8
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに
GitHubのアップデートは頻繁に行われていて、GitHub上でのコードレビューもしやすくなってきてますね！
ただ、やっぱり手元のエディタで動かしながら確認したいことってありますよね

でも、そのために現在の作業を `stash` したり、ブランチを切り替えて `npm install` し直したりするのは正直面倒です…

そんな課題を解決するのが [gtr (Git Worktree Runner)](https://github.com/coderabbitai/git-worktree-runner) です！
（なぜ `gtr` と略すのかはわかってないです😇）

## なぜ gtr なのか？

Git標準の `git worktree` を使えばフォルダ単位でブランチを展開できます。
しかし、コマンドが長くて覚えにくいのが難点でした。
`gtr` はそれをシンプルにラップし、さらに便利機能を追加したツールです。

### コマンドがシンプル
`git gtr new <branch>` だけで作成完了

### 多様なエディタに対応
VS Code, Cursor, Zed などを指定して即座に開ける

### AIエージェントとの連携
Claude Code や Codex などを、そのブランチで独立して起動可能

### 環境構築の自動化
`npm install` の自動実行や `.env` ファイルのコピーも設定可能

## 基本的な使い方

使い方は非常にシンプルです。

```bash
# 1. レビュー用ブランチのワークツリーを作成
git gtr new feature/new-ui

# 2. 設定したエディタで開く
git gtr editor feature/new-ui

# 3. CLIベースのコーディングエージェントなどで開く（任意）
git gtr ai feature/new-ui

# --- レビュー終了後 ---

# 4. サクッと削除
git gtr rm feature/new-ui
```

これだけで、メインの作業環境を一切汚さずにレビュー環境を構築・破棄できます！
もちろん通常の開発でも便利に使えます。

## 便利な連携設定

`gtr` はリポジトリごとに柔軟な設定が可能です。

### 1. エディタとAIツールの設定

お好みのツールをデフォルトに設定できます。
特にAIツールをブランチごとに独立して走らせられるのは、並行作業時に強力です。

```bash
# エディタ (code, cursor, zed など)
git gtr config set gtr.editor.default code

# AIツール (claude, aider, copilot など)
git gtr config set gtr.ai.default claude
```

### 2. 面倒なセットアップを自動化 (Hooks)

ワークツリー作成後に `npm install` などを自動で走らせることができます。「開いたらすぐ動く」状態が作れるので必須級の設定です。

```bash
git gtr config add gtr.hook.postCreate "npm install"
```

### 3. ファイルの自動コピー

`.env.example` などを新しいワークツリーに自動コピーする設定も可能です。

```bash
git gtr config add gtr.copy.include "**/.env.example"
```

## さらに楽にするTips

頻繁に使うなら、以下の関数を `.zshrc` や `.bashrc` に入れておくと楽でした！

```bash
gtr() {
  git gtr new "$1" && git gtr editor "$1"
}
```

これで `gtr feature/branch-name` と打つだけで、用意できます！

## おわりに
読んでいただきありがとうございます！
作業の中断もコンフリクトも気にせず、快適なレビューができて便利ですね！
ぜひ試してみてください！

## 参考

https://github.com/coderabbitai/git-worktree-runner
