---
title: プロジェクト単位で Codex 設定を切り替えるTips
tags:
  - AI
  - codex
  - ChatGPT
  - LLM
private: false
updated_at: '2025-10-24T08:15:58+09:00'
id: 6deecce0a7ecaa92a411
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
最近、Codex CLIをよく使っています！

ただ、`$CODEX_HOME` （デフォルトは `$HOME/.codex` ）の設定ファイルやカスタムプロンプトしか見てくれなくて不便だったので自分なりの解消方法を書いてみました！

## 方法

`~/.zshrc` に以下のように記載します↓

これにより、`codex` を実行した時に現在のプロジェクトディレクトリの `.codex` を見るようになり、設定ファイルやプロンプトなどもコピーされます！

```bash:~/.zshrc
codex() {
  local _codex_home="$(pwd)/.codex"
  mkdir -p "$_codex_home"

  # これが無いとChatGPTログインがプロジェクト毎に必要になる
  if [ -e "$HOME/.codex/auth.json" ] && [ ! -e "$_codex_home/auth.json" ]; then
    ln -sfn "$HOME/.codex/auth.json" "$_codex_home/auth.json"
  fi
  # 設定ファイルやカスタムプロンプトはお好みで
  if [ -e "$HOME/.codex/config.toml" ] && [ ! -e "$_codex_home/config.toml" ]; then
    cp "$HOME/.codex/config.toml" "$_codex_home/config.toml"
  fi
  if [ -d "$HOME/.codex/prompts" ] && [ ! -d "$_codex_home/prompts" ]; then
    mkdir -p "$_codex_home/prompts/"
    cp -a "$HOME/.codex/prompts/"* "$_codex_home/prompts/"
  fi
  # 今いるプロジェクトディレクトリの設定を使うようにする
  CODEX_HOME="$_codex_home" command codex "$@"
}
```

楽なのでまとめてしまってますが、1回実行すればOKなコピー系の処理は切り出した方が良いかもしれません😇


## 注意点
- 公式のベストプラクティスとかではないため使用する際にはご注意ください
- コピーもできるので個人的には困ってないですが `$HOME/.codex` は見なくなります
- `.codex` ディレクトリには公開しない方が良い情報も生成されるので、git管理から除外にしましょう！（`.gitignore` より、`.git/info/exclude` や `core.excludesFile` で除外するようにしておくとより安心かもしれません）

```
**/.codex/*
# 設定やプロンプトを含めるかはお好みで
!**/.codex/config.toml
!**/.codex/prompts
```

## おわりに
読んでいただきありがとうございます！少しでも参考になれば幸いです🙏
IssueやPRは出ているみたいなので公式対応されるかもしれませんが、それまではこの設定を使おうと思っています！
