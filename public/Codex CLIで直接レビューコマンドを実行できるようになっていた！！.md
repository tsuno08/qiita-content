---
title: Codex CLI で直接レビューコマンドを実行できるようになっていた！！
tags:
  - OpenAI
  - codex
  - AI
  - LLM
  - コードレビュー
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Codex CLI の `/review` コマンドをよく使っているのですが、最近のアップデートで `/review` 相当を直接実行するコマンドが追加されていました！

これまでは `/review` コマンドを対話モード内で実行していましたが、ついに `codex review` コマンドで直接実行できるようになります！

## 何が嬉しいか

コマンドで直接実行できるため、自動化ワークフローに組み込みやすいという点が嬉しいです！

これまでは、人が `codex` コマンドを実行し、対話モードに入ってから `/review` を叩く必要がありました。
今回のアップデートにより、別のコーディングエージェントや Git のフックなどから直接 `codex review` を呼び出すことが可能になり、開発プロセスへの統合がよりスムーズになります。

## 対応バージョン

v0.65.0 （あるいはそれ以下）で追加されていたようです。
それらしき GitHub の PR も見つけました👀

https://github.com/openai/codex/pull/7444

公式ドキュメントの方にはまだ記載が見つけられませんでしたが、ヘルプコマンドで確認できました。

（余談）Claude にあるような Skills が experimental で追加されてました！

## 使い方

基本的な使い方は以下の通りです。

```bash
codex review [OPTIONS] [PROMPT]
```

### 引数

- `[PROMPT]`: カスタムのレビュー指示を指定します。`-` を指定すると標準入力から読み込みます。

### オプション

ヘルプコマンド (`codex review --help`) で確認できたオプションは以下の通りです↓

| オプション | 説明 |
| :--- | :--- |
| `-c, --config <key=value>` | デフォルトでは設定ファイル (`~/.codex/config.toml`) が使われるがその値の上書き用。<br>ドット区切り (`foo.bar.baz`) でネストされた値を指定できます。<br>値は TOML としてパースされ、失敗した場合は文字列として扱われます。<br><br>例:<br>・ `-c model="o3"`<br>・ `-c 'sandbox_permissions=["disk-full-read-access"]'`<br>・ `-c shell_environment_policy.inherit=all` |
| `--uncommitted` | ステージングされた変更、されていない変更、追跡されていない変更を含めてレビューします。 |
| `--base <BRANCH>` | 指定したベースブランチに対する変更をレビューします。 |
| `--enable <FEATURE>` | [機能](https://github.com/openai/codex/blob/main/docs/config.md#feature-flags)を有効にします（繰り返し指定可）。`-c features.<name>=true` と同等です。 |
| `--commit <SHA>` | 指定したコミットによる変更をレビューします。 |
| `--disable <FEATURE>` | [機能](https://github.com/openai/codex/blob/main/docs/config.md#feature-flags)を無効にします（繰り返し指定可）。`-c features.<name>=false` と同等です。 |
| `--title <TITLE>` | レビューの要約に表示するコミットタイトル（オプション）。 |

## 使用例

### 作業中の変更をレビューする

まだコミットしていない変更を含めてレビューしたい場合は `--uncommitted` を使います。

```bash
codex review --uncommitted
```

### 特定のブランチと比較してレビューする

`main` ブランチとの差分をレビューしたい場合など。

```bash
codex review --base main
```

### 他のコーディングエージェントから呼び出す用

他のコーディングエージェントなどから呼び出す場合は、出力を減らしたいケースが多いと思います。
思いついた方法をいくつか紹介します。（もっと良い方法があれば教えてください！）

#### thinking ログを減らす

そのままだと大量の thinking ログが出力されてしまうので、`-c hide_agent_reasoning=true` オプションを付けて呼び出すと thinking ログが減らせて良さそうです！

```bash
codex review --uncommitted -c hide_agent_reasoning=true
```

#### コメントのみ出力する

`hide_agent_reasoning=true` を設定しても、大量のコマンド実行ログが出力されてしまいます。
コメントのみ出力したい場合は、以下のように `sed` コマンドなどでレビューコメント以降だけを抽出するのが良さそうです。

:::note warn
出力のフォーマットが将来変わる可能性があるため、あくまで暫定的な方法としてご参考ください。
:::

```bash
codex review --uncommitted 2>&1 | sed -l -n '/Review comment:/,$p'
```

以下は実行した際の出力例です↓

```
Review comment:

- [P1] xxx
tokens used
40,720
```

## おわりに

読んでいただきありがとうございます！
コマンド一発でレビューが走らせられるのは非常に便利ですね！
pre-commit hook などに仕込んで、コミット前に自動で AI レビューを受けたり、他のコーディングエージェントと組み合わせて、修正のループを回したりみたいな使い方も面白そうです。
ぜひ活用してみてください！

## 参考リンク

https://github.com/openai/codex
