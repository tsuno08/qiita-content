---
title: Datadog MCPを自作してみた感想色々
tags:
  - AI
  - Datadog
  - MCP
  - LLM
private: false
updated_at: '2025-05-17T11:40:43+09:00'
id: 2c90962af7923724b243
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

Datadogでのログ検索をLLMを通じてより手軽に、そして対話的に利用したいと思い、公式のMCPもなかったので開発しました。

※この記事は、開発の過程や得られた知見をAIの支援を受けながらまとめたものです。

## 開発の進め方

開発の初期段階からLLMを積極活用する方針としました。

具体的には、VSCodeのGitHub Copilotのエージェントモードを使用しました。

モデルはClaude 3.7 Sonnetを用いました。

LLMにMCPの仕様を理解させるために、以下のドキュメントを情報源として提供しました。

- [modelcontextprotocol.io提供の`llms-full.txt`](https://modelcontextprotocol.io/llms-full.txt)
    - MCPの概念、アーキテクチャ、コアコンセプト（リソース、プロンプト、ツール等）、トランスポート層、デバッグ方法など、MCPの全体像を網羅。
- [MCPのLLM活用チュートリアル](https://modelcontextprotocol.io/tutorials/building-mcp-with-llms)
    - LLMとの協調開発の具体的な進め方の参考に。
- [Datadog APIリファレンス](https://docs.datadoghq.com/ja/api/latest/)
    - 連携対象であるDatadog APIの仕様確認。
- [公式GitHub MCPサーバー実装](https://github.com/modelcontextprotocol/servers/tree/main/src/github)
    - ディレクトリ構成や実装パターンの参考に。
- [MCP TypeScript SDKのREADME.md](https://github.com/modelcontextprotocol/typescript-sdk/blob/main/README.md)
    - SDKの具体的な利用方法の把握。

これらの情報を基に、LLMに対して具体的な要件を伝えました。
そして、エージェントモードでコードの雛形やロジックの生成を依頼し、それを叩き台として開発を進めるというスタイルを取りました。

## 開発で良かった点

### LLMへの情報提供による効率化

- 上記の各種ドキュメントをLLMに提供することで、MCPの基本設計やDatadog APIの仕様に関する理解を深めさせることができました。
- これにより、開発の初期段階での学習コストを大幅に削減できました。特に`llms-full.txt`はMCPの全体像を掴む上で非常に有効でした。

### Zodによるスキーマ定義の恩恵

- デフォルト値の設定
    - `z.string().optional().default("*")` のように、オプションパラメータにデフォルト値を容易に設定できました。
- 説明の付与
    - `.describe("検索クエリ")` のように、各パラメータに人間可読な説明を追加することで、LLMがツールの機能を正確に理解する助けとなりました。
- enumによる入力値の制限
    - `z.enum(["timeseries", "total"])` のように、特定のパラメータが取りうる値を列挙型で厳密に定義しました。
    - LLMによる不正な値生成を防ぐ上でも効果的でした。

### MCP Inspectorによる動作確認の容易さ

- 開発したMCPサーバーの動作検証には、[MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector) が非常に役立ちました。
- `npx @modelcontextprotocol/inspector` コマンド一つで手軽に起動できます。
- 対話形式で各ツールを呼び出したりできるので、MCPサーバーの各機能が期待通りに動作することを簡単に確認できました。

## 開発中に遭遇した主な問題と解決策

### "Error: spawn node ENOENT"

- 原因
    - MCPサーバーを起動する際に、`mcp.json`などの設定ファイルで指定したnodeコマンドのパスがシステムに見つけられなかった。
- 解決策
    1. nodeコマンドのパスを環境に依存しない絶対パスで指定することで、問題を解消した。

### "Tool [ツール名] does not have an implementation registered."

- 原因
    - クライアントアプリケーション（例えばVS Code）が以前のツール定義をキャッシュしている場合に発生することがあった。([GitHub MCP Server Issue #177](https://github.com/github/github-mcp-server/issues/177)に関連情報あり)。
- 解決策
    1. VS Codeのコマンドパレットから `MCP: Clear Cached Tools` コマンドを実行し、VS Code自体を再起動した。
    2. これにより、クライアント側のキャッシュがクリアされ、最新のツール定義が読み込まれるようになった。

### 日付パラメータの取り扱い

- 原因
    - Datadog MCPへのリクエスト時、期間を指定するパラメータに日付フォーマットの文字列を直接渡そうとしたが、期待通りに解釈されなかった。
- 解決策
    1. UNIXタイムスタンプ（数値型）で渡すように変更し、ある程度改善した
    2. それでも意図した期間でデータがフィルタリングされない問題が続き、当初はLLMが生成するタイムスタンプが古すぎることが原因で、Datadog側のデータ保持期間外のデータをリクエストしていると推測
    3. そのため、現在の日時を取得するだけの `date mcp` を作って使用した
    4. これにより、正しい期間を指定できるようになり、ユーザーの利便性も大幅に向上した

### LLM連携におけるコンテキストウィンドウとエラーログの影響

- 原因
    - 日付生成を担うMCPサーバー（`date mcp`）と、今回開発した`datadog mcp`を連携させようとした際に、意図しないループ状態に陥った。具体的には、`date mcp`が生成した時刻情報を`datadog mcp`が受け取り、その結果を基に再び`date mcp`を呼び出す処理が繰り返された。
- 解決策
    1. 出力がシンプルすぎることがLLMの誤解を招いている可能性を考え、より詳細な形に変更したが、ループは解消されなかった。
    2. Datadog MCPサーバーのレスポンスに含まれるDatadog APIからの警告メッセージなどをLLMがエラーとして捉え、処理を異常継続させている可能性を疑い、レスポンスの整形や警告の除外、英語への統一などを試みたが、根本的な解決には至らなかった。
    3. レスポンスの`content`配列を分割した方がいいのかと思ったが、[公式のGitHub MCPサーバー実装の例](https://github.com/modelcontextprotocol/servers/blob/main/src/github/index.ts)を参照したが、積極的に分割しているケースは限定的であり、今回の問題解決に必須ではないと判断した。
    4. Datadog APIのレスポンスは非常に冗長な場合があり、これがコンテキストウィンドウを圧迫し、LLMの判断を誤らせる可能性が考えられた。検証としてログ取得期間を短く（例「過去30分以内」）設定すると、ループせずに正常に動作した。
    5. その結果から、Datadog MCPサーバー側で返す情報をより厳選し、LLMの判断に必要なコア情報のみを渡すように改修した結果、無限ループの問題は改善された。

## 作ったもの

一応載せときます、コードは綺麗じゃないですが…

https://github.com/Nozomuts/datadog-mcp

## まとめ

特に`llms-full.txt`のような包括的なドキュメントをLLMに提供することで、ほとんどのコード生成をAIに任せることができました。

MCP（ツール）は純粋な拡張以外にもAIの行動を制限するのにも使えるなと思いました。

結構手軽に作れるので今後も何か作ってみようかなと思います。
