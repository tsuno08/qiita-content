---
title: Claude 4が来た！簡潔にまとめてみました
tags:
  - AI
  - LLM
  - Claude
private: false
updated_at: '2025-05-23T10:37:57+09:00'
id: bec21680143806c92df2
organization_url_name: null
slide: false
ignorePublish: false
---
Claude 4、ついに来ましたね〜
GitHub CopilotにSonnetだけではなくOpusも来てた！！
これから使ってみようと思います（プレミアムリクエスト来る前に使い倒しましょう）

https://github.blog/changelog/2025-05-22-anthropic-claude-sonnet-4-and-claude-opus-4-are-now-in-public-preview-in-github-copilot/

# 概要
以下のページを日本語訳で見るだけでもかなり掴めると思います🙇‍♂️
この後は自分で理解しながらまとめていきます👀

https://www.anthropic.com/news/claude-4

# Claude Opus 4とClaude Sonnet 4について
- Opusは最も強力なモデルで世界最高のコーディングモデルとのこと
- Sonnetはコーディングと複雑な問題解決に優れたモデル
- SWE-benchでGemini 2.5 Pro、o3超え！現状最高？
> ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/40d64105-c2fa-404a-bc8a-7c123b72b16b.png)

## 変更点確認
- コンテキストウィンドウは変化なし（200k）
- Sonnetはレイテンシも変化なし
  - モデル比較表での表記が全て速いになっている
  - ただ英語の方だとOpusは中程度に速いとなっているので、少し遅そうです
- 料金およびレートリミットも据え置き
  - Sonnet、Opusともに3.7・3.5の時と全く同じでした！これは嬉しい！

https://docs.anthropic.com/ja/docs/about-claude/models/overview

# Claude Code一般公開
多分嬉しいです、まだ全然使えてないですが…
- Claude Code SDKでアプリケーションの統合可能
  - GitHub Actionsで実行可能
- VSCodeとJetBrainsとのネイティブ統合をサポート

https://docs.anthropic.com/ja/docs/claude-code/sdk

# 4つの新しいAPI機能
全てベータ機能を含み、ベータヘッダーの指定が必要です

https://docs.anthropic.com/ja/api/beta-headers

## コード実行ツール
サンドボックス化された環境でPythonコードを実行できる

データ分析、視覚化を作成、複雑な計算、アップロードされたファイルを処理したりなど色々できる

https://docs.anthropic.com/ja/docs/agents-and-tools/tool-use/code-execution-tool


<details><summary>curlで計算を依頼する実行例（折りたたみ）</summary>

```bash:curl
curl https://api.anthropic.com/v1/messages \
    --header "x-api-key: $ANTHROPIC_API_KEY" \
    --header "anthropic-version: 2023-06-01" \
    --header "anthropic-beta: code-execution-2025-05-22" \
    --header "content-type: application/json" \
    --data '{
        "model": "claude-opus-4-20250514",
        "max_tokens": 4096,
        "messages": [
            {
                "role": "user",
                "content": "Calculate the mean and standard deviation of [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]"
            }
        ],
        "tools": [{
            "type": "code_execution_20250522",
            "name": "code_execution"
        }]
    }'
```

</details>

### 料金
- トークン使用量とは別
- 実行時間は最低5分でセッション時間あたり$0.05

## MCPコネクター（ベータ機能）
MCPクライアントを実装せずにMCPサーバーに接続できる

### 制限事項
- MCP仕様の機能セットのうち、ツール呼び出しのみがサポート
- サーバーはHTTPを通じて公開されている必要があり、ローカルのSTDIOサーバーには直接接続できない
- Amazon BedrockおよびGoogle Vertexではサポートされていない

ツール呼び出しができるだけでもかなり便利そう！
HTTPを通じて公開する需要が上がりそうですね👀

<details><summary>curlでの実行例（折りたたみ）</summary>

```bash:curl
curl https://api.anthropic.com/v1/messages \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "anthropic-beta: mcp-client-2025-04-04" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 1000,
    "messages": [{"role": "user", "content": "hoge"}],
    "mcp_servers": [
      {
        "type": "url",
        "url": "https://example-server.modelcontextprotocol.io/sse",
        "name": "example-mcp",
        "authorization_token": "YOUR_TOKEN"
      }
    ]
  }'
```

</details>



https://docs.anthropic.com/ja/docs/agents-and-tools/mcp-connector

## ファイルAPI（ベータ機能）
リクエストごとにコンテンツを再アップロードすることなく、Anthropic APIで使用するファイルをアップロードおよび管理できる

https://docs.anthropic.com/ja/docs/build-with-claude/files

### できること
- ファイルのアップロード
- ファイル一覧の取得
- ファイルメタデータの取得
- ファイルの削除
- ファイルのダウンロード
  - コード実行ツールによって作成されたファイルのみダウンロードできる

<details><summary>curlでのファイルのアップロードの実行例（折りたたみ）</summary>

```bash:curl
curl -X GET "https://api.anthropic.com/v1/files/file_abc123/content" \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "anthropic-beta: files-api-2025-04-14" \
  --output downloaded_file.txt
```

</details>

### 料金
- 基本無料
- Messagesリクエストで使用されるファイルコンテンツは入力トークンとして課金

### 制限
- ストレージ制限
  - 最大ファイルサイズ： ファイルあたり32 MB
  - 総ストレージ： 組織あたり100 GB
- レートリミット
  - 約1分あたり100リクエスト

## 拡張プロンプトキャッシュ（ベータ機能）
プロンプトのキャッシュ保存期間（TTL）を標準の5分間とするか、追加料金で1時間のTTLに延長するかを選択できる

価格は従来のキャッシュ書き込みの1.6倍ほど

https://docs.anthropic.com/ja/docs/build-with-claude/prompt-caching#1

### 使用ケース
  - 5分よりも頻度が低いが、1時間ごとよりも頻繁に使用される可能性があるプロンプトがある場合
  - レイテンシーが重要で、フォローアップのプロンプトが5分を超えて送信される可能性がある場合
  - レート制限の利用率を向上させたい場合
    - キャッシュヒットはレート制限から差し引かれないため

### 使用方法
- ベータヘッダーとして `extended-cache-ttl-2025-04-11` を含める必要あり
- `cache_control` 定義の `ttl` に `1h` を指定する

<details><summary>curlでの実行例（折りたたみ）</summary>

```bash:curl
curl https://api.anthropic.com/v1/messages \
  -H "content-type: application/json" \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-beta: extended-cache-ttl-2025-04-11" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-opus-4-20250514",
    "max_tokens": 1024,
    "system": [
      {
        "type": "text",
        "text": "hoge",
        "cache_control": {
            "type": "ephemeral",
            "ttl": "1h"
        }
      }
    ],
    "messages": [
      {
        "role": "user",
        "content": "hoge"
      }
    ]
  }'
```

</details>



https://docs.anthropic.com/ja/api/beta-headers


# おわりに
ここまで読んでくださりありがとうございます！
単純なモデルの性能向上以外にもAPIなどかなり機能が拡張されていますね〜
正直モデルの性能がどれくらいかは掴めていないので、これから触っていきます！
Claude Codeも使っていきたい
