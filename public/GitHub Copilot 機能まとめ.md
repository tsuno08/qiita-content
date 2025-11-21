---
title: GitHub Copilot 機能まとめ
tags:
  - Git
  - GitHub
  - AI
  - VSCode
  - githubcopilot
private: false
updated_at: '2025-04-11T19:40:34+09:00'
id: f077570ecb74cd7cf398
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
学習も兼ねて、GitHub Copilotの機能についてまとめてみました！
また、日本語の拡張機能を入れている関係で機能名に変なところあるかもしれません🙇
※この記事はMacかつVSCodeの使用を前提としています
※プレビュー・試験段階の機能の利用にはご注意ください

:::note warn
2025/04/11現在の情報を元に書いてます
誤った情報や変更された項目もある可能性があるため、ご注意ください🙏
:::



# 機能

## コード補完

コードを書き始めたり、コメントを書くと、次に入力される可能性のあるコードを予測して、エディター上に提案してくれる

- 薄いグレーのゴーストテキストで表示される
- `Tab` キーで受け入れ可能

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/13fc975b-54c8-4118-90ed-5cf61a26cc38.png)

✨Tips

- `⌘` + `→` (macOS) を使用して、単語ごとまたは行ごとに提案を受け入れることができる
- 補完は開いているファイルから適切な候補を作成するため、関連ファイルを開いておくと良い

### Next Edit Suggestions

 現在行っている編集に基づいて、次に行う可能性のある編集箇所とその内容を予測して提案してくれる

- 関連する変更が予測される場所にガターに矢印が表示される
- `Tab`キーを繰り返し押すことで、提案された編集箇所をナビゲートし、受け入れることができる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/cce33ceb-6dfa-4b82-a785-ed4822793f08.png)

- 使用方法
    - VS Codeの設定で `github.copilot.nextEditSuggestions.enabled` を有効にする
    - 補完は右下のCopilotアイコンから簡単にオンオフの切り替えができる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8d73538f-f9bb-4059-a2ee-460c9fc6cd7e.png)

## Copilot Chat

コードについて質問したり、自然言語を使ってプロジェクト全体を編集したりできる

Copilot Chatには以下の3つのインターフェースがある

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/9c816a8f-dcdc-4b9e-9802-86e90ea4c7ce.png)

✨Tips

- チャット履歴を関連性のある状態に保つと良い

### インラインチャット

#### エディタ内

- 現在のコードに対する質問や編集指示を直接行うのに適している
- 使用方法
    - ショートカットキー（ `⌘` + `I` ） or Copilotアイコン or 右クリックから

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/69af1d49-c58d-412c-bb16-0950c892372b.png)

#### ターミナル

- ターミナルでの作業中に実行したいコマンドが不明な場合などコマンドの提案を受けられる
- 提案されたコマンドはそのままターミナルに挿入して実行できる
- 使用方法
    - ショートカットキー（ `⌘` + `I` ） or 右クリックから

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/522f8418-04b7-4493-bd04-472ce6fb010a.png)

### クイックチャット

- 画面上部に表示される一時的なチャット
- あまり使わない（クイックチャットで出来ることはチャットビューでも出来るため）
- 使用方法
    - ショートカットキー（ `⇧` + `⌥` + `⌘` + `L` ） or Copilotアイコンから

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f760b13d-4529-4d15-b1ff-b51e54e2501d.png)

### チャットビュー

- サイドバーに常設されるチャットインターフェース
- 複数の質問と回答を続ける会話に適している
- 使用方法
    - ショートカットキー（ `^` + `⌘` + `I` ） or Copilotアイコンから

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/05c23fbd-a362-4b8b-a4f1-5a11bf010be2.png)

チャットビューには3つのモードがある

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/07793faf-4be5-489c-b061-bc3fe0683554.png)

| モード | 用途 |
| --- | --- |
| 質問 | コードに関する質問、機能の実装方法、VS Codeの設定方法など、様々な疑問の回答<br>実装方針の壁打ち |
| 編集 | 複数のファイルに跨るシンプルな変更 |
| エージェント | 自律的に計画し、コード編集、ターミナルコマンド提案/実行させる<br>問題解決を連続的に試みる<br>MCPを利用 |

編集モードに切り替えるとチャットセッションが切り替わってしまうため、基本的には使わない（個人的に）

#### MCP: Model Context Protocol（プレビュー）

- MCPとは？
    - アプリケーションが LLM にコンテキストを提供する方法を標準化するオープンプロトコル
    - AI アプリケーション用の USB-C ポートのようなもの
    - 参考: https://modelcontextprotocol.io/introduction
- settings.json から追加&MCPサーバーを起動可能
    - settings.json の部分に薄く「起動」ボタンが表示されるのでクリック
    - エージェントモードでのみ「🛠️」アイコンが表示され、使用可能な項目が選択できる
    
    ```json
    {
    	"mcp": {
    		"servers": {
    			"playwright": {
    				"command": "npx",
    				"args": [
    					"@playwright/mcp@latest"
    				]
    			}
    		}
    	}
    }
    ```

:::note warn
使用する際は書きリポジトリの以下のReference Serversや公式が出しているものの利用を強く推奨します
https://github.com/modelcontextprotocol/servers/?tab=readme-ov-file#-reference-servers
:::

例）

以下のような入力をすると実行許可を求めるので続行を押す

✨Tips

- 「続行」の左のvを押すと自動で許可するかなどの設定ができる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/32b8ee8e-5639-4a1e-8def-f4a03d3a7fe6.png)

以下のように実行される↓

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/486cda13-55de-4e33-bf4d-3ac2b68da208.png)

### チャットに指定可能な項目

#### **Chat participant（チャット参加者）**

指定した対象に関する質問などができる

- チャット入力欄で `@` に続けて指定する
- チャットビュー、クイックチャットでのみ使用可能

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/b2031912-4c0a-47a1-aec4-2f00b00f4bdf.png)

| **Chat participant** | 説明 |
| --- | --- |
| `@workspace` | ワークスペースに関する質問など |
| `@vscode` | VSCodeに関する質問など |
| `@github` | GitHubのWeb検索、コード検索、Enterpriseのナレッジベースに基づいた情報などを得る |
| `@terminal` | ターミナルで何かを行う方法を確認 |

✨Tips

- 項目は入れている拡張機能によって増えたりする

#### **スラッシュコマンド**

特定の意図をCopilotに伝えるためにスラッシュコマンドを使用できる

- 使用方法
    - チャット入力欄で `/` に続けてコマンドを入力する

✨Tips

- 一部、Chat participantとの組み合わせも可能

| スラッシュコマンド | 説明 | チャットビュー（質問モード） | インラインチャット（エディタ内） | インラインチャット（ターミナル） | クイックチャット |
| --- | --- | --- | --- | --- | --- |
| `/clear` | チャット履歴削除 | ✅ |  |  | ✅ |
| `/doc` | 現在のファイルまたは選択したメソッド/関数に対するドキュメントコメントを生成 | ✅ | ✅ |  | ✅ |
| `/edit` | アクティブなエディタで選択したコードを編集 |  | ✅ |  |  |
| `/explain` | 選択したコードについて説明 | ✅ | ✅ | ✅ | ✅ |
| `/generate` | 新しいコードを生成 |  | ✅ |  |  |
| `/help` | Copilotの使い方について知る | ✅ |  |  | ✅ |
| `/fix` | 選択したコードの問題点やコンパイラー/リンターのエラーの修正案を提案 | ✅ | ✅ |  | ✅ |
| `/fixTestFailure` | 失敗しているテストに対する修正案を提案 | ✅ |  |  | ✅ |
| `/search` | VSCode内でコード検索を生成 | ✅ |  |  | ✅ |
| `/setupTests` | プロジェクトに適したテストフレームワークのセットアップ手順やVS Code拡張機能の推奨を提案 | ✅ |  |  | ✅ |
| `/startDebugging` | デバッグ構成ファイル（launch.json）の生成とデバッグセッションの開始を支援（試験段階） | ✅ |  |  | ✅ |
| `/tests` | 現在のファイルまたは選択したメソッド/関数に対するテストコードを生成 | ✅ | ✅ |  | ✅ |
| `/new` | 新しいワークスペースやファイルを生成 | ✅ |  |  | ✅ |
| `/newNotebook` | 新しいJupyter Notebookを生成 | ✅ |  |  | ✅ |

#### **コンテキスト**

Copilot Chatに質問する際、コンテキストを追加することで、より的確で関連性の高い回答を得ることができる

- 使用方法
    - チャット入力欄の横にある「コンテキストを追加する」（📎）をクリック
    - `#` 記号を入力して、利用可能なコンテキスト変数の一覧から選択

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/fc75b24b-01d9-44c6-9529-906160a556a1.png)

| コンテキストの種類 | 説明 |
| --- | --- |
| エディタを開く | 現在開いているファイルをまとめて追加 |
| Codebase（`#codebase`） | Copilotが自動的に関連性の高いファイルを検索してコンテキストとして含める|
| Fetch Web Page（`#fetch`） | URLを指定してWeb ページからコンテンツを取得 |
| Find Test Files | 添付されたファイルのテストとなるファイルを取得？ |
| Find Usages（`#usages`） | 選択内容の使用状況を分析 |
| Get VS Code API References（`#vscodeAPI`） | VSCode拡張機能の開発について質問する際に使用 |
| Git Changes（`#changes`） | Gitの変更履歴 |
| Test Failure（`#testFailure`） | テスト失敗情報 |
| シンボル（`#sym`） | ワークスペース内の特定のシンボル（関数、クラスなど） |
| スクリーンショット ウィンドウ | ウィンドウのスクショが添付される（Copilot Visionが必要？） |
| ターミナルの最後のコマンド（`#terminalLastCommand`） | ターミナルで最後に実行したコマンドの出力 |
| ターミナルの選択（`#terminalSelection`） | ターミナルで選択したテキスト |
| プロンプト | Reusable prompts で設定したものを選択 |
| フォルダ（`#folder`） | 特定のフォルダーとその中のファイル |
| 問題（`#problem`） | 問題（Problems）パネルに表示されている特定の問題 |
| 検索結果（`#searchResults`） | 検索ビューの検索結果 |
| ファイル（`#file` ） | ワークスペース内の特定のファイルを指定 |

✨Tips

- `#` の方では使用できないコンテキストもあるので、📎の方をおすすめ
- #codebase を指定すると、Copilotが適切なファイルを自動的に見つけられる
- 設定で `github.copilot.chat.codesearch.enabled` とより良くなる
- 送信ボタンの右のvを押すと「#codebase で送信」を選択できる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/30c67d94-9f1e-4ccd-a5af-9d17d211fb24.png)

## 生成内容のカスタマイズ

### Custom Instructions

チームのワークフロー、ツール、プロジェクト固有の詳細に合わせて、GitHub Copilotの応答をカスタマイズできる

- チャット、クイックチャット、インラインチャットでのプロンプトに自動的に適用される
    - コード補完には適用されない
- 用途別に分かれている
    - ワークスペース全体で共有するコード生成の際に使用する指示
        - `github.copilot.chat.codeGeneration.useInstructionFiles` をオンにする
        - `.github/copilot-instructions.md`
    - コード生成の際に使用する指示(試験段階)
        - settings.jsonでテキスト or ワークスペース内のファイルを指定
    - テストコード生成の際に使用する指示(試験段階)
        - settings.jsonでテキスト or ワークスペース内のファイルを指定
    - レビューの際に使用する指示(プレビュー)
        - settings.jsonでテキスト or ワークスペース内のファイルを指定
    - コミットメッセージ生成の際に使用する指示(試験段階)
        - settings.jsonでテキスト or ワークスペース内のファイルを指定
    - プルリクエストのタイトルと説明を生成する際に使用する指示(試験段階)
        - settings.jsonでテキスト or ワークスペース内のファイルを指定

例）

```json
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "Always add a comment: 'Generated by Copilot'."
    },
    {
      "text": "In TypeScript always use underscore for private field names."
    },
    {
      "file": "code-style.md" // import instructions from file `code-style.md`
    }
  ],
```

✨Tips

- 指示は短く、シンプルな文にすると良い

### Reusable prompt files（プレビュー）

`.github/prompts` ディレクトリに作成したMarkdownファイル（`.prompt.md`）のプロンプトをチャットで利用できる機能

- 使用方法
    - VS Code設定 `chat.promptFiles` を `true` に設定
    - チャットのコンテキストで「プロンプト」から作成したMarkdownファイルを選択

## スマートアクション

プロンプトを記述せずに実行できる様々なアクション

### 右クリック

エディタ内で対象のコードを選択後、右クリック > Copilotからアクセスできる

選択範囲のレビューに加えて、`/explain`、`/fix`、`/doc`、`/tests` などのコマンドと同じ内容を実行できる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/71072bdc-641a-4fde-aac6-18f13e95432e.png)


変数や関数名も、右クリック > シンボルの名前変更で候補を提案させて変更できる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/dab8ba3c-1ec0-4c5b-a7c7-665f703b7ca6.png)

### 左パネル

#### **ソース管理ビュー**

コミットメッセージ入力欄やPR作成画面に表示される「✨」アイコンをクリックすることで、、AIを活用してメッセージや説明文を生成できる

ソース管理ビューにある「💬」アイコンやPR作成画面に表示される「Copilotコードレビュー」ボタンをクリックすることでIによるコードレビューが受けられる

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/fcac7097-f630-4be5-a6c4-0c03aad48e5e.png)

✨Tips

- Custom Instructionsを設定することでフォーマットに合わせたメッセージを生成可能

#### **検索ビュー**

通常の検索に加えて、テキストの意味に基づいた検索も可能

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8ea43ff2-72a6-4394-9a4e-70a65da440a8.png)

### クイック修正

エラーなどの内容の説明や、修正が1タップで出来る

コード内やテストのエラー箇所や、ターミナルに表示される「✨」アイコンをクリック

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/1ec6ca4a-eee1-4f2d-933a-7b0198f18f4d.png)

## デフォルトで使用可能なAIモデル

- GPT-4o（デフォルト）
- Claude Sonnet 3.5
- Claude Sonnet 3.7
    - おすすめ
    - 全てのモードで使用可能
- Gemini 2.0 Flash
    - トークン数が多い時や速度重視の場合におすすめ
    - エージェントモードで使用不可
- Claude Sonnet 3.7 Thinking
    - 実装プランなどをじっくり考えさせたい時におすすめ
    - エージェントモードで使用不可

# 終わりに
プレビューや試験段階の機能が多いですが、GitHub Copilotもかなり便利になってきましたね〜
少しでも読んだ人の役に立てたら幸いです！

# 参考

https://docs.github.com/ja/copilot

https://code.visualstudio.com/docs/copilot/overview

https://modelcontextprotocol.io/introduction
