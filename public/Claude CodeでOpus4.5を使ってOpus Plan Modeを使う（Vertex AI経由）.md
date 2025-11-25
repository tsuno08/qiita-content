---
title: Claude CodeでOpus4.5を使ってOpus Plan Modeを使う（Vertex AI経由）
tags:
  - AI
  - LLM
  - Claude
  - ClaudeCode
private: false
updated_at: '2025-11-25T13:39:24+09:00'
id: 3301e31cc212faa518e4
organization_url_name: null
slide: false
ignorePublish: false
---
::: note warn
Vertex AI経由での利用で試しました🙏
別の利用方法だとうまくいかないかもしれません
:::

## はじめに
Claude Opus 4.5が出ましたね！
今回から以前より値段が安くなり使いやすくなりました！

https://www.anthropic.com/news/claude-opus-4-5

今回はVertex AI経由でClaudeを利用する際にOpus Plan Modeの利用で困ったので記録しておきます🙇‍♂️

## Opus Plan Modeとは？
結構前に追加された、PlanモードでOpusを使用し、実装ではSonnetなどを使用するモードのことです。

Sonnet 4.5が出てからは `/model` の選択肢から消えたらしく、`/model opusplan` や設定ファイルに指定することで利用できます！

https://zenn.dev/tomoikawa/articles/732b79d5f10bb2

## 問題
デフォルトだとOpus 4.1が使われており、有効化しておらずエラーが出ていました

```
  ⎿ API Error: 404 {"error":{"code":404,"message":"Publisher Model 
    `projects/xxx/locations/global/publishers/anthropic/models/claude-opus-4-1@20250805` was not found or your project does not have access to it. 
    Please ensure you are using a valid model version. For more information, see: 
    https://cloud.google.com/vertex-ai/generative-ai/docs/learn/model-versions","status":"NOT_FOUND"}}
```


## 解決方法

1. `/model opusplan` でOpus Plan Modeを使う設定をする
1. `ANTHROPIC_DEFAULT_OPUS_MODEL`  に `claude-opus-4-5@20251101` を指定

で使えました！

## おまけ: ステータスラインでモデルを表示するとわかりやすい

ステータスラインという機能で、Claude Code インターフェイスの下部にカスタム表示ができます。

公式のサンプルのものを使用してモデル名を表示してみました！

https://code.claude.com/docs/en/statusline

```json:.claude/settings.json
{
// その他の設定
    "type": "command",
    "command": "input=$(cat); MODEL_DISPLAY=$(echo \"$input\" | jq -r '.model.display_name'); CURRENT_DIR=$(echo \"$input\" | jq -r '.workspace.current_dir'); echo \"[$MODEL_DISPLAY] 📁 ${CURRENT_DIR##*/}\""
	}
}
```

以下のようにPlanモードではOpus 4.5が使われているのが確認できました

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/e2785636-2821-4327-b673-adb15b4cf470.png)


![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/9d34af1f-8c3e-4836-bcc6-b4a9075acb20.png)

他にも例えばコンテキスト残量も表示できるみたいで便利そうですね！

https://zenn.dev/pnd/articles/claude-code-statusline


## 参考

https://code.claude.com/docs/en/model-config#environment-variables

