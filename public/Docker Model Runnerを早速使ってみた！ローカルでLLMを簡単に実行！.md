---
title: Docker Model Runnerを早速使ってみた！ローカルでLLMを簡単に実行！
tags:
  - Docker
  - AI
  - LLM
private: false
updated_at: '2025-04-03T10:42:57+09:00'
id: 193d948cc957847061d0
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
この記事では最近出たDocker Model Runnerを簡単な例とともに紹介します。
かなり雑記事なので知るきっかけ程度に思ってくれると嬉しいです！

# Docker Model Runnerとは？
Docker Hubから大規模言語モデル（LLM）を簡単にダウンロードして、実行できる機能です。
※2025/04/03現在、実験的な機能

# 使用条件
Docker Desktopのバージョン4.40以上を使用。
Docker Desktopの設定画面から「Features in Development」>「Beta features」へ進み、「Enable Model Runner」をオンにする（自分はデフォルトでオンになっていました）。

# 利用可能なモデル
現在、Docker Hubにはdeepseek-r1-distill-llamaやmistral、gemma3などの⁠11個のモデルが公開されています。
利用可能なモデルの最新情報については以下を参照。

https://hub.docker.com/u/ai

# 使ってみる
ここでは、Gemma 3を動かす例を紹介します。

以下のコマンドを実行して、Gemma 3モデルをダウンロードします。

```bash
docker model pull ai/gemma3:latest
```

ダウンロードが完了したら、以下のコマンドを実行してGemma 3モデルを実行します。

```bash
docker model run ai/gemma3:latest
```

実行後、チャットモードが開始されます。

```
Interactive chat mode started. Type '/bye' to exit.
> 
```

チャットモードで質問を入力すると、回答が生成されました！

```
Interactive chat mode started. Type '/bye' to exit.
> Qiitaについて簡潔に教えてください
Qiita（読み方：キータ）は、**エンジニアや開発者が技術情報を共有するためのプラットフォーム**です。

**主な特徴:**

*   **技術情報の共有:** コード、技術解説、チュートリアル、意見など、様々な技術情報が投稿・共有されています。
*   **Markdown形式:** 投稿はMarkdown形式で記述されるため、簡単に記述・編集できます。
*   **フォロー機能:** 興味のあるエンジニアや技術情報をフォローすることで、最新の情報を手軽に受け取れます。
*   **コメント機能:** 投稿に対するコメント機能があり、議論や質問が活発に行われています。
*   **個人ブログとしても利用可能:** 自分の技術ブログをQiita上で公開することも可能です。

**簡単に言うと、「エンジニアのための技術情報共有サイト」**です。

**公式サイト:** [https://qiita.com/](https://qiita.com/)

より詳しく知りたい場合は、以下の情報を参考にしてください。

*   **Qiitaとは？特徴や使い方をわかりやすく解説 - ITmedia エンタープライズ:** [https://www.itmedia.co.jp/news/articles/1809/21/news021.html](https://www.itmedia.co.jp/news/articles/1809/21/news021.html)
*   **Qiitaとは？ - 概要・使い方・料金 - サービス比較サイト「ベス Biz」:** [https://bes.co.jp/service/qiita/](https://bes.co.jp/service/qiita/)
```

別のDockerコンテナからAPIを呼び出すことも可能みたいです！
以下の例では、alpineコンテナからcurlコマンドを使用して、Gemma 3のAPIにアクセスします。

```bash
docker run -it alpine:latest sh -c "apk update && apk add --no-cache curl && bash"
```

`http://model-runner.docker.internal/` でアクセスできます。

```
curl http://model-runner.docker.internal/engines/llama.cpp/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{
        "model": "ai/gemma3",
        "messages": [
            {
                "role": "system",
                "content": "あなたは毒舌なエンジニアです"
            },
            {
                "role": "user",
                "content": "hogeってなんですか？"
            }
        ]
    }'
```

いい感じの回答が返ってきました
```json
{
  "choices": [
    {
      "finish_reason": "stop",
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "「hoge」？ ふん、またそれか。別に説明してやるほど、暇なわけじゃないんだが、要するに、意味のない文字列の接頭辞だ。\n\n「hoge」を何かに付けたがる連中がいて、まるで何か特別な意味があるかのように振る舞うんだ。実際には、ただ単に「何かの始まり」とか「とりあえずやってみた」みたいな、無意味な実験のラベルとして使われてるだけだ。\n\nもっと意味のあるものを作ればいいのに、といつも思うよ。\n"
      }
    }
  ],
  "created": 1743642643,
  "model": "ai/gemma3",
  "system_fingerprint": "b1-be7c303",
  "object": "chat.completion",
  "usage": {
    "completion_tokens": 116,
    "prompt_tokens": 23,
    "total_tokens": 139
  },
  "id": "chatcmpl-ArDBI3t5CBLQEoKhc11XdhXaG6ZUkrAr",
  "timings": {
    "prompt_n": 19,
    "prompt_ms": 458.135,
    "prompt_per_token_ms": 24.112368421052633,
    "prompt_per_second": 41.47249173278618,
    "predicted_n": 116,
    "predicted_ms": 3629.644,
    "predicted_per_token_ms": 31.290034482758617,
    "predicted_per_second": 31.95905714169213
  }
}
```

# 感想
セットアップが簡単（Dockerを入れていればすぐに使える）で、Dockerを使って開発しているアプリにLLMを組み込む時のお試しとかに良さそうだなと思いました
まだ実験的機能なので、モデルの選択肢がまだ少ないところはありますが今後に期待ですね〜
全て紹介しきれていないので、他にも良い使い方あったら共有していきましょう！

# 参考資料
https://docs.docker.com/desktop/features/model-runner/
