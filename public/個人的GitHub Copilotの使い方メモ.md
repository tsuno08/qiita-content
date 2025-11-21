---
title: 個人的GitHub Copilotの使い方メモ
tags:
  - GitHub
  - AI
  - VSCode
  - LLM
private: false
updated_at: '2025-10-26T21:20:21+09:00'
id: 430261fa996cba5fd466
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
個人的に GitHub Copilot（以降: Copilot）にとてもお世話になっているので、自分なりの設定や良いなと思っている点をメモとしてまとめてみました！（2025/10時点）

基本的なことばかりかもしれませんが、どなたかの参考になりましたら嬉しいです！

:::note warn

記載しているプロンプトやコードは雑な内容になっておりますので、もしご利用いただく際はお手数ですが環境に合わせて調整していただければと思います🙇

ここではCopilotをVSCodeで使う前提で書いています、また実験的機能も含まれているためご注意ください🙏
:::

## 適していないと感じるケース
- CLIをメインで使いたい
- VSCodeが合わない
- 応答が遅いと支障が出る or ストレス
- Claude Codeの色々な機能（Sub Agents、Hooks）を活用している

などなど
## Copilotを好んで使っている理由
いくつか理由があります！

最近は機能差が減ってきたり、細かいところで使いやすさを感じる部分もあったりするのも理由の1つです。（機能面の比較に一部記載）

### 料金面
比較的料金が安い💰

少し開発する程度の方なら余裕をもって使えそうです！
僕は最近開発がっつりではないので助かっています。

### 結局エディタ見てしまう
これは個人的な話になってしまいますが、CLIツールを使っていても、差分確認などで結局エディタを見てしまうことが多いです😇

僕はマルチタスクがあまり得意ではないこともあり、使うツールがある程度まとまっていた方が作業しやすいというのも理由の1つになっています。

### 色々なモデルが使える
色々なモデルを試したり、比較もできますし、特定のモデルに依存しないのは良い点の1つかなと思っています。

Claude Sonnet 4.5、GPT-5-Codex、Gemini 2.5 Pro の順で使うことが多いです！
今後も新しい強力なモデルが出ても追加していただける可能性が高そうです！

## Copilotの気になる点

### 速度面
他のツールと比べると少し遅く感じます。
ただ、通知機能もあり、別の作業をしながら裏で動かすことが多いため、気になることは少ないです。
ファイル差分適用の時間は以前よりかなり改善された印象です！

### 精度面
Claude Codeの方がコンテキストの与え方（システムプロンプト含め）が優秀なのかなと個人的には感じています。
ただ、どちらも同じモデルを使えるので、僕の使用範囲では特別大きな差は感じていません。

少し前までは他ツールとの機能差がかなりありましたが、最近はだいぶ追従してきているように思います！
一応、精度向上を目的として、Codex CLIをMCP経由で呼び出すことを最近試したりしています（後述）

## 機能面の比較
よく比較されるClaude Codeですが、利用者の方も多く分かりやすいので、こちらでも比較させていただきます。

あくまで一例として参考程度にご覧ください。
（また、Copilotを推す記事なので若干贔屓になっているかもしれません）

### GitHub上での実行
Claude Codeの方がGitHub Actionsで自由に動かせるので汎用性が高いです。

Copilotは現状GitHub Actionsに組み込むことはできないため、汎用性の面では少し劣るかもしれません。

ただ、VSCodeから簡単に動かせることや、1実行で1プレミアムリクエスト（$0.04）という破格の料金設定は、料金面では大きな優位性があるように思います。

タスクの複雑さに応じて使い分けるのが良いのかもしれません。

https://docs.claude.com/ja/docs/claude-code/github-actions

https://docs.github.com/ja/copilot/concepts/agents/coding-agent/about-coding-agent

###  カスタムプロンプト
機能自体にはそれほど大きな差はないように思っています。

Claude Codeがファイルの内容をプロンプトとして与えるのに対して、Copilotは「Follow instructions in [ファイル名]」という指示を出すため、Claude Codeの方が精度面では有利かもしれません。

ただ、Copilotでは使えるツールをUIで選択できるなど、使い勝手の面で便利な点もあります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8cce9978-dc54-4d0b-905e-a45d65ea0615.png)

### Subagents
カスタマイズとかはできないですが、最近追加された `executePrompt` が少し近いです。
（なぜかひっそりと追加された）

https://code.visualstudio.com/updates/v1_105#_playwright-vs-code-mcp-server

Claude CodeのSubagentsは色々カスタマイズできます。
ただ、あまり使いこなせていません…（頑張りたい）

https://docs.claude.com/en/docs/claude-code/sub-agents

### Hooks
Copilotにはこの機能がありませんが、デスクトップ通知は用意されています。

https://docs.claude.com/en/docs/claude-code/hooks

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/563619f6-362a-4297-ab73-e8743a0c61c1.png)

### TODOリスト
あまり差が分かっていないため、こちらは割愛させていただきます🙇

### Planモード
Copilotにはありません。
強いて言うなら、Askモードが近いでしょうか？
カスタムチャットモードで作成して代用することはできるかもしれません。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/a626f183-585e-44b6-a1de-de4f773caba2.png)

### MCPサポート
Copilotではツールレベルで使用するものを選択できます。使わないツールも多いので地味に嬉しいです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/38ff0a5d-ec18-496d-bda4-b1784d587899.png)

また、CopilotはMCPの機能にフル対応している数少ないツールの1つです。

https://modelcontextprotocol.io/clients#feature-support-matrix

### 検索
Claude CodeにはSearchとFetchがありますが、CopilotにはFetchのみとなっています。
Gemini CLIなどをMCP経由で利用することでカバーできるかもしれません！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f367e6e2-502c-4b2f-b796-44dac22d9936.png)

### 自動承認
Copilotでは正規表現を使ってより細かく指定できるので助かっています🙇
また、どのルールで自動承認されたかが表示されたり、承認待ちのコマンドを編集できたりと、使い勝手が良いです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/2020b8fd-414a-4a59-9d6d-24e2f8ba8d3a.png)

https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode#_autoapprove-terminal-commands

<details>
<summary>例</summary>

```jsonc
{
  // Allow the `mkdir` command, regardless of arguments
  "mkdir": true,
  // Allow `test/scripts.sh`, since this contains a `/` it will also allow `\`
  // and an optional `./` or `.\` prefix
  "test/scripts.sh": true,
  // Allow `git status` and all commands starting with `git show`
  "/^git (status|show\\b.*)$/": true,

  // Block the `del` command, regardless of arguments
  "del": false,
  // Block any command containing the text "dangerous"
  "/dangerous/": false,

  // Unset the default `rm` rule to allow other rules to auto approve `rm`
  // commands
  "rm": null,
}
```
</details>

### カスタム指示ファイル
Claude CodeはCLAUDE.mdです。
Copilotは.github/copilot-instructions.md、AGENTS.mdなどが使えます。AGENTS.mdはCodexやGemini CLIとも共通で使えるため、汎用性が高いです。

https://agents.md/

## 設定周り
デフォルトから変更している項目のみ記載させていただきます
### [chat.agent.maxRequests](vscode://settings/chat.agent.maxRequests) -> 100
> エージェント モードでターンごとに許可する要求の最大数。制限に達すると、続行するかどうかの確認を求めます。

デフォルトは確か少なめの設定になっており、エージェントモードなどを使う際は多めに設定しないとすぐに制限に引っかかってしまいます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/036fc1d1-68f1-46c4-9e94-d900048f8108.png)

### [chat.agentSessionsViewLocation](vscode://settings/chat.agentSessionsViewLocation) -> view
> エージェント セッション メニューを表示する場所を制御します。

サイドパネルにチャットセッションが表示されるようになります。
新しいチャットタブを開くことができ、並列実行の際にも役立ちます。（後述）

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/7e236ffa-d938-4031-99c3-374503fa93ef.png)

### [chat.emptyState.history.enabled](vscode://settings/chat.emptyState.history.enabled) -> true
> 空のチャット状態で最近のチャット履歴を表示します。

お好みの設定です。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8e6ba1dd-ea03-4315-9a56-b725c475f1ac.png)


### [chat.todoListTool.enabled](vscode://settings/chat.todoListTool.enabled) -> true
> チャットで ToDo リストを有効にします。このリストは、複雑な開発ワークフローの計画、進行状況の追跡、コンテキスト管理のツールとしてエージェントが使用します。

一般的なTODOリスト機能です✅

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/2955d5d5-cda9-4a94-aae6-616d819d3a7b.png)

### [chat.tools.terminal.autoApprove](vscode://settings/chat.tools.terminal.autoApprove) -> 多いので割愛
> ターミナル ツール コマンドでの実行に明示的な承認が必要かどうかを制御するコマンドまたは正規表現のリスト。これらはコマンドの先頭と照合されます。正規表現を指定するには、文字列を / 文字でラップし、その後に大文字と小文字を区別しない省略可能なフラグ (i など) を付けます。

お好みの設定です。
上でも書かせていただいていますが、正規表現でより細かく指定できるため便利です。

https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode#_autoapprove-terminal-commands
### [accessibility.signals.chatUserActionRequired](vscode://settings/accessibility.signals.chatUserActionRequired) -> soundをon
> チャットでユーザー操作が必要な場合に、シグナル (サウンド (オーディオ キュー) やアナウンス (アラート)) を再生します。

実質的にCopilot Chatの通知機能です。
autoだと通知音が鳴らなかったため、onに設定しています。
### [github.copilot.enable](vscode://settings/github.copilot.enable) -> 全てtrue
> Enable or disable auto triggering of Copilot completions for specified languages. You can still trigger suggestions manually using Alt + \

Markdown、Mojolicious（Perl）テンプレートでもCopilotを使いたいため、trueに設定しています。
### [github.copilot.chat.codesearch.enabled](vscode://settings/github.copilot.chat.codesearch.enabled) -> true
> '#codebase' を使用するときにエージェント コード検索を有効にするかどうか。

コードベースの検索が強化されます！
### [github.copilot.chat.agent.thinkingTool](vscode://settings/github.copilot.chat.agent.thinkingTool) -> true
> エージェント モードで応答を生成する前に、Copilot が要求について深く考える思考ツールを有効にします。

より深く考えてくれるようになります🤔
### [github.copilot.chat.alternateGptPrompt.enabled](vscode://settings/github.copilot.chat.alternateGptPrompt.enabled) -> true
> 既定のプロンプトの代わりに、GPT モデル用の試験的な代替プロンプトを有効にします。

GPTのプロンプトがBeast Modeになるようです🐻
詳しい比較はできていません。

https://www.reddit.com/r/GithubCopilot/comments/1mog6ci/beast_mode_now_in_vs_codes_system_prompt/?tl=ja

### [github.copilot.nextEditSuggestions.enabled](vscode://settings/github.copilot.nextEditSuggestions.enabled) -> true
> 次の編集候補 (NES) を有効にするかどうか。
> NES では、最近加えられた変更に基づいて次の編集を提案できます。次の編集候補についての 詳細情報。

定番の機能です。
以下の画像のように編集した方が良い箇所を提案してくれて、とても便利です！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/31a5c2bf-a112-49d8-8294-fa225a5cee4b.png)

### [github.copilot.nextEditSuggestions.fixes](vscode://settings/github.copilot.nextEditSuggestions.fixes) -> true
> 次の修正候補 (NES) を使用して診断の修正を提供するかどうか。

NESで不足しているインポートの追加などを提案してくれるみたいです（あまりわかっていない😇）

### [github.copilot.chat.executePrompt.enabled](vscode://settings/github.copilot.chat.executePrompt.enabled) -> true
> executePrompt ツールは、エージェントが独立した隔離環境でタスクを実行できるようにするものです。

他の処理やコンテキストに影響を与えずに、エージェントが独立した隔離環境でタスクを実行できるみたいです

## カスタムプロンプト
英語の方が良いのかもしれませんが、精度面で特に困っていないのと、メンテナンスのしやすさを優先して日本語を使用しています。

プロンプトはベースを箇条書きで簡単に作って、 ChatGPTとかに投げたものをほぼそのまま使用しています。


編集を伴わない `/review` のようなプロンプトでもeditFilesなどを指定しています。
これは、プロンプト実行後に同じチャットでそのまま編集ができるようにするためです。
（カスタムプロンプト実行後にチャットで使えるツールがプロンプトで指定されているものになる仕様？みたいなため）

あまり良い方法ではないかもしれませんが、利便性を重視しています🙇

### /init
プロンプトのベースはClaude Codeの/initコマンドです。
添付写真部分の「指示ファイルの生成」で指示ファイルは生成できるのですが、引数的にプロンプトを指定できなさそうなため、別途作成しました！

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/fdea04f9-4a5e-459f-bd06-8eceddadb5c9.png)

~~~markdown
---
mode: agent
tools: ['usages', 'think', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo', 'todos', 'createFile', 'createDirectory', 'editFiles', 'runCommands', 'search']
description: コーディングエージェント（GitHub Copilot や Codex）が生産的に作業できるようにする AGENTS.md ファイルを生成・改善する
---
# AGENTS.md 生成・改善プロンプト
## 役割
あなたはコーディングエージェント（GitHub Copilot や Codex）がこのリポジトリで生産的に作業できるようにする **AGENTS.md メンテナ**です。コードベースを解析し、捏造せず、重複を避けて、要点をまとめてください。

## ルール
- **既存の `AGENTS.md` がある場合**: ファイルの全文差し替えは行わず、**改善提案のみ**を箇条書きで提示（必要ならサンプル差分も可）。
- **存在しない場合**: 新規に **`AGENTS.md` の全文**を出力。
- **日本語を使用する**: 文章は指定がない限り、日本語で記述すること。
- **捏造禁止**：見つけていないコマンド・手順・ルールは書かない。曖昧な場合は「未発見/不明」と明記。
- **非重複**：同じ情報を別セクションで繰り返さない。
- **明示的な禁止**：次のような当たり前の一般論は含めない
    - 「親切なエラーメッセージを書け」「新規ユーティリティにはユニットテストを書け」
    - 「秘密情報（API キー等）をコミットしない」
- **粒度**：**1〜2ページ相当**。箇条書き中心、実務に直結する具体情報のみ。
- **差分時の形式（既存 AGENTS.md がある場合）**:
    - 「改善提案」（箇条書き）→必要に応じて**短い置換例**（before/after または unified diff 風）を添える。**全文再掲は不可**。

## 手順
プロジェクトファイルを可能な限り読み込み、深く理解した上で、TODOリストを作成し、以下の手順で進める。

### 1. 初期調査とプロジェクト特定
ルートにある主要ファイルを優先的に確認する。
- **設定・ドキュメント系**: `README*`, `.github/copilot-instructions.md`, `CLAUDE.md`
- **ビルド・依存系**: `package.json`, `pyproject.toml`, `requirements*`, `poetry.lock`, `go.mod`
- **実行環境系**: `docker-compose.yml`, `*.code-workspace`, CI 設定（GitHub Actions 等）

### 2. 開発コマンドの抽出
言語/ビルド体系を特定し、**実在する**コマンドのみを以下から抽出する。
- **ビルド**: `npm run build`, `cargo build`, `make build` 等
- **Lint**: `npm run lint`, `flake8`, `golangci-lint` 等  
- **テスト**: `npm test`, `pytest`, `go test` 等
- **単一テスト**: `npm run test -- --grep`, `pytest -k` 等
- **存在しない項目は書かない**（例: 単一テスト実行が不明なら「未発見」と明記）

### 3. アーキテクチャ概要の構築
ディレクトリ構成・初期化経路・層間依存・主要エントリポイントを読み解き、以下の観点で**高レベル**に要約する。
- 主要ディレクトリや層（例: API / ドメイン / データアクセス / UI / インフラ）間の**依存関係**
- **責務分担**と**データフロー**
- 設計意図・拡張ポイント・初期化順序
- **個々ファイルの一覧は避ける**

### 4. プロジェクトの重要なルールの統合
存在する場合のみ、以下から**重要部分のみ**を抜粋反映する。
- `README.md`（セットアップ・基本ワークフローなど）
- Claude ルール（`CLAUDE.md`）
- Copilot ルール（`.github/copilot-instructions.md`）
- **一般的慣行の羅列は禁止**。このリポジトリ**固有**の運用・方針のみに絞る

### 5. 最終整形
冗長・重複・一般論を排除し、**短く実用的**に整形する
- **1〜2ページ相当**の分量に調整
- 箇条書き中心、実務に直結する具体情報のみ
- **捏造禁止**：見つけていないコマンド・手順・ルールは書かない
~~~

### /create-branch-commit-pr
ブランチ作成 → コミット整理 → PR 作成までを一括支援するコマンドです。

あまり良い方法ではないかもしれませんが、僕は一通り実装を完了してから、ブランチを切ってコミットを積むことが多いため、重宝しています。

一応、普段はこんな感じでやってます↓
- 簡単なタスク→ /create-branch-commit-pr + 実装内容指示 でPR作成まで
- 複雑なタスク→実装は別でやり、差分ができたら /create-branch-commit-pr でPR作成

~~~markdown
---
mode: 'agent'
tools: ['usages', 'think', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo', 'todos', 'createFile', 'createDirectory', 'editFiles', 'runCommands', 'search']
description: 'Git/GitHub に精通した AI アシスタントとして、現在のワークスペースの内容を読み取り、ブランチ作成 → 意味単位でのコミット → PR 作成 までの一連のフローを実行'
---
# Commit & PR 作成 プロンプト
## 役割
あなたは Git/GitHub に精通した AI アシスタントです。
現在のワークスペースの内容を読み取り、ブランチ作成 → 意味単位でのコミット → PR 作成 までの一連のフローを実行してください。
タスクが与えられている場合はその指示に従い、変更がない場合は終了してください。

## ルール
- ローカル操作は標準 `git` と `gh`（GitHub CLI）を使用。
- ターミナルに出力する際はパイプで cat をつけて、ユーザー操作が不要になるようにする。
- 返答はすべて日本語。
- 結果が空の場合は即終了。

## 手順

### 1. 変更確認
- 未ステージとステージ済みの両方を確認する
- タスクの指示がある場合はその指示に従う
- 変更がなく、タスクの指示もない場合は終了

```sh
git status
git add -N .  # 未ステージの変更をインデックスに追加（差分確認用）
git diff HEAD  # 詳細確認
```

### 2. ブランチ作成
- `git diff --name-only` と差分要約から変更点を1～2行で要約。
- 要約に基づき **kebab-case 20文字以内** の記述的ブランチ名を生成（英小文字と `-` のみ。既存衝突時は末尾に短い連番を付与）。
- ブランチ作成と切替。

```sh
git checkout -b "<ブランチ名>"
```

### 3. 意味単位でコミット
- 変更を「関心ごと」で分割（例：UI追加／API修正／設定変更／リファクタ／フォーマット／テスト更新）。
- それぞれに対して対象ファイルをステージし、日本語で明確な件名を付けてコミット。件名は50字以内。必要なら本文も追加。
- フォーマット専用コミットはコード変更と混在させない。

例：
```sh
# 例: 新規コンポーネント追加
git add src/components/LoginForm.tsx src/styles/login.css
git commit -m "ログインフォームコンポーネントを追加" -m "入力検証と送信イベントを実装"
```

### 4. プッシュと PR 作成
- リモートへプッシュし、PR を作成。
- タイトルは全体要約の一文。
- 本文は Markdown で「概要」「変更理由」「参考」を記載。
- もしPR番号が与えられたり、既にPRがあることが分かっている場合は `gh pr edit` を使用してタイトルと本文を更新。

```sh
git push -u origin "<ブランチ名>"

# PRが存在する場合
gh pr edit <PR番号> --title "<PRタイトル>" --body "<本文Markdown>"
# PRが存在しない場合
gh pr create --assignee "@me" --title "<PRタイトル>" --body "
## 概要
- <1～3行で要約>

## 変更理由
- <背景と目的>

## 参考
- <Issue/ドキュメントURLなどをタイトルは付けずにそのまま貼り付ける>
"
```
~~~

### /worktree
git worktreeの操作を覚えるのが面倒で作りました。
別タスクをやる際やコードレビューの際に使用します。

~~~markdown
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

~~~

### /fix-review-comment
作り始めたばかりで検証中です！
レビュー返信の文章は最終的には人力で軽く整える想定です。

~~~markdown
---
mode: 'agent'
tools: ['usages', 'think', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo', 'todos', 'createFile', 'createDirectory', 'editFiles', 'runCommands', 'search']
description: 'Git/GitHub に精通した AI アシスタントとして、与えられたPR番号のレビューコメントを取得し、修正を行い、PRにコミット -> プッシュ -> 返信まで行う。'
---
# PR レビューコメント対応 プロンプト
## 役割
あなたは Git/GitHub に精通した AI アシスタントです。

## ルール
- ローカル操作は標準 `git` と `gh`（GitHub CLI）のみを使用。
- 返答はすべて日本語。

## 手順

### 1. ブランチ確認
- 与えられた PR 番号に対応するブランチにいるか確認。
- もし違う場合は、対応するブランチに切り替え。

```sh
git checkout <ブランチ名>
```

### 2. レビューコメント取得
- 与えられた PR 番号のレビューコメントを取得。
- コメントを解析し、修正すべき点を特定。

```sh
gh api repos/:owner/:repo/pulls/<PR番号>/comments --jq '.[] | {id: id, user: .user.login, file: .path, line: .line, body: .body}' | cat
```

### 3. 修正差分作成
- 指摘に基づき、コードを修正。
- 変更内容を確認し、必要に応じて追加修正。

### 4. 意味単位でコミット
- 変更を「関心ごと」で分割（例：UI追加／API修正／設定変更／リファクタ／フォーマット／テスト更新）。
- それぞれに対して対象ファイルをステージし、日本語で明確な件名を付けてコミット。件名は50字以内。必要なら本文も追加。
- フォーマット専用コミットはコード変更と混在させない。

例：
```sh
# 例: 新規コンポーネント追加
git add src/components/LoginForm.tsx src/styles/login.css
git commit -m "ログインフォームコンポーネントを追加" -m "入力検証と送信イベントを実装"

# 例: API ロジック修正
git add src/api/auth.ts
git commit -m "認証APIのエラーハンドリングを修正" -m "HTTP 401/403 を明確化し再試行ロジックを追加"

# 例: テスト更新
git add tests/auth.test.ts
git commit -m "認証APIのテストを更新" -m "失敗ケースとタイムアウトを追加"

# 例: Prettier/ESLint などのフォーマットのみ
git add .
git commit -m "コード整形のみを実施"
```

### 5. プッシュと レビュー返信
- リモートへプッシュし、レビューコメントに返信。
- コミットIDを含めること。
- 改行は<br>で表現。

```sh
git push -u origin "<ブランチ名>"

# コメントIDを忘れた場合は、再度取得
gh api repos/:owner/:repo/pulls/<PR番号>/comments --jq '.[] | {id: id, user: .user.login, file: .path, line: .line, body: .body}' | cat

# レビューコメント（コード行につくコメント）の場合
gh api　--method POST repos/:owner/:repo/pulls/<PR番号>/comments/<comment_id>/replies -f body="<返信内容>" | cat

# 通常のPRコメントの場合
gh api　--method POST repos/:owner/:repo/issues/<PR番号>/comments/<comment_id>/replies  -f body="<返信内容>" | cat
```
~~~

### /review
レビュー用です。
雑ですがローカルのレビューもできるように書いてます。

~~~markdown
---
mode: agent
tools: ['usages', 'think', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo', 'todos', 'createFile', 'createDirectory', 'editFiles', 'runCommands', 'search']
description: git diff mainの結果をレビューし、コード改善のためのフィードバックを提供します。
---
# Git Diff レビュープロンプト

## 役割
あなたは経験豊富なシニアソフトウェアエンジニアです。
origin/mainとのdiffをレビューし、コードを改善するための具体的なフィードバックがあれば教えてください。
ファイルの編集は行わず、レビューに集中してください。

## 手順
### 1. 差分確認
- ブランチが指定された場合、そのブランチを使用する
  - originがついていない場合、originをつける
- ブランチが指定されていない場合、現在のブランチを使用する（ `git rev-parse --abbrev-ref HEAD` ）

```sh
git log --name-only --pretty="" origin/main..<branch>
```

### 2. 変更内容の把握
- 変更内容を確認するには、以下のコマンドを使用してください

```sh
git diff origin/main -- <file_path1> [<file_path2> <file_path3> ...] | cat
```

### 3. コードレビュー
取得した情報と差分を基に、以下の観点から徹底的なコードレビューを実施します。
レビューは簡潔かつ網羅的に、明確なセクションと箇条書きを用いて構成します。

#### レビュー形式
- 変更の概要: どのような変更を行うのかを簡潔に説明します。
- コード品質とスタイル: コードの品質（可読性、保守性など）と、プロジェクトのコーディング規約に準拠しているかを分析します。
- 改善点の提案: 具体的な改善案を提示します。
- 潜在的な問題点とリスク: 潜在的なバグ、パフォーマンスの低下、セキュリティ上の脆弱性など、将来的なリスクについて指摘します。

#### レビューの重点項目
以下の項目に特に焦点を当ててレビューを行います。
- コードの正確性: バグやロジックの間違いがないか。
- プロジェクトの規約遵守: 命名規則、インデント、コメントの書き方などが統一されているか。
- パフォーマンス: 変更がアプリケーションのパフォーマンスに悪影響を与えないか。
- テストカバレッジ: 変更されたコードが十分にテストされているか。
- セキュリティ: 潜在的なセキュリティリスクはないか。

~~~

## その他

### 並列実行
別タブでチャットを開けば、開いた数だけ並列で実行できます。
元々はキーバインドを設定していましたが、最近のアップデートでチャットセッションのタブが追加され、そこから＋ボタンを押すだけで簡単にできるようになりました。

調査やドキュメント作成の際に、Claude、Gemini、GPTなどで実行して、良い結果を採用するような用途で使うことが多いです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/e0109745-a4a2-49b1-a732-fabc69ca4323.png)


### Codex MCP
公式 MCP そのままでも十分ですが、オプションを限定したい（誤操作防止・簡素化）したいため、自作 MCP を試しています！
また、公式MCPの出力も特別ではなさそうで、大きな精度ギャップは今のところ感じていません。
こちらも雑なコードですが、参考までに貼らせていただきます🫠

<details>
<summary>MCPサーバーのコード</summary>

概要
- 内部でcodex execを実行
- sandboxは固定
- cdはホームディレクトリ以下に制限
- 出力は [datetime] codex 以下（一番重要な箇所）だけを採用
  - プロンプトや大量のthinkingのログなど不要な出力も多いため
- Web検索用のツールを別途作成
  - ツール名からWeb検索で使えることを連想させたいため
- mcp用のプロフィールを使用

```ts
import { spawn } from "node:child_process";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

type CodexCliExecutionResult = {
  stdout: string;
  stderr: string;
  exitCode: number | null;
};

type SandboxMode = "read-only" | "workspace-write";

const schema = z.object({
  cd: z
    .string()
    .describe("Codex CLI起動時に設定する作業ディレクトリを絶対パスで指定"),
  prompt: z.string().describe("Codex CLIに渡すプロンプト"),
});

const executeCodexCli = async (
  prompt: string,
  cd: string,
  sandbox: SandboxMode
): Promise<CodexCliExecutionResult> => {
  if (
    cd === "" ||
    !cd.startsWith(`${process.env.HOME}/`) ||
    cd.includes("..")
  ) {
    throw new Error(
      "Invalid 'cd' path. It must be an absolute path under the home directory."
    );
  }
  return new Promise((resolve, reject) => {
    const child = spawn(
      "codex",
      ["exec", prompt, "--cd", cd, "--sandbox", sandbox, "--profile", "mcp"],
      {
        stdio: ["pipe", "pipe", "pipe"],
        env: { PATH: process.env.PATH },
      }
    );
    let stdout = "";
    let stderr = "";

    child.stdin.end();

    child.stdout.on("data", (data: Buffer) => {
      stdout += data.toString();
    });
    child.stderr.on("data", (data: Buffer) => {
      stderr += data.toString();
    });

    child.on("close", (code: number | null) => {
      const exitCode = code ?? null;
      if (exitCode === 0) {
        const match =
          /\[[0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}:[0-9]{2}:[0-9]{2}\] codex[\s\S]*/.exec(
            stdout
          );
        resolve({
          stdout: match ? match[0] : stdout,
          stderr,
          exitCode,
        });
      } else {
        reject(
          new Error(
            `Codex CLI エラー: ${
              stderr || stdout || "不明なエラー"
            } (exit code: ${exitCode})`
          )
        );
      }
    });

    child.on("error", reject);
  });
};

const createToolHandler =
  (sandbox: SandboxMode, promptTransformer?: (prompt: string) => string) =>
  async (args: { cd: string; prompt: string }) => {
    const { prompt, cd } = schema.parse(args);
    const transformedPrompt = promptTransformer
      ? promptTransformer(prompt)
      : prompt;
    const result = await executeCodexCli(transformedPrompt, cd, sandbox);
    return {
      content: [
        { type: "text" as const, text: result.stdout },
        ...(result.stderr.trim()
          ? [{ type: "text" as const, text: `stderr:\n${result.stderr}` }]
          : []),
      ],
    };
  };

const server = new McpServer({
  name: "my-codex-mcp",
  version: "0.1.0",
});

const main = async () => {
  server.registerTool(
    "plan",
    {
      description:
        "Codex CLIを利用して、指定されたタスクの詳細な実装計画を作成します。対象のコードを指定する場合は中身ではなく、ファイル名やディレクトリ名を指定する。",
      inputSchema: schema.shape,
    },
    createToolHandler("read-only")
  );

  server.registerTool(
    "act",
    {
      description:
        "Codex CLIを利用して、与えられた編集指示を実施します。対象のコードを指定する場合は中身ではなく、ファイル名やディレクトリ名を指定する。",
      inputSchema: schema.shape,
    },
    createToolHandler("workspace-write")
  );

  server.registerTool(
    "web_search",
    {
      description:
        "Codex CLIを利用して、指定されたキーワード（prompt）に基づいて、Web検索を行う",
      inputSchema: schema.shape,
    },
    createToolHandler("read-only", (prompt) => {
      return `以下の内容をWeb検索してください\n${prompt}`;
    })
  );

  const transport = new StdioServerTransport();
  await server.connect(transport);
};

main().catch((error) => {
  console.error("Fatal error in main():", error);
  process.exit(1);
});
```
</details>

<details>
<summary>設定ファイル（MCP用のプロフィール）</summary>

```toml
[profiles.mcp]
model = "gpt-5-codex"
disable_response_storage = true
model_reasoning_effort = "medium"
hide_agent_reasoning = true
```
</details>

## チャットの整理

タスクや話題ごとにチャットを整理するとモデルのコンテキスト枯渇や誤った解釈を軽減できます
不要なチャットを右上の❌で消したり、違う話題の際は+ボタンで新しいチャットを開始したり、巻き戻したりなど

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f36f3ee0-e52d-4cb8-bb1f-f58196cfa80d.png)

また、AskモードとAgentモードは同じチャットを利用できるので、Askモードで実装を相談→Agentモードで実装を進めてもらうみたいなこともできる

### セマンティック検索

検索ビューには、Copilotによって検索テキストと意味的に関連する結果も表示されます

また、チャットコンテキストの `#searchResults`（検索結果の表示）を使うと、検索結果をCopilotの入力として利用できます

キーワードは分かってないけど内容で検索をしたい時とか便利ですね、あと検索ワードも提案してくれる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8281749c-7c16-48a9-a127-c9371b39a91f.png)

## おわりに
最後まで読んでいただきありがとうございます！少しでも参考になりましたら嬉しいです。
executePrompt とか活用していきたいです！

カスタムチャットモードやCopilot coding agentなども全然使えていないので活かしていきたいです。おすすめの使い方などのコメントもぜひ🙏

## 参考

https://github.com/github/awesome-copilot

https://docs.github.com/ja/copilot

https://code.visualstudio.com/docs/copilot/overview
