---
title: GitHub Actionsに低コストな「ubuntu-slim」ランナー登場！ ubuntu-latest と比較
tags:
  - GitHub
  - CICD
  - GitHubActions
private: false
updated_at: '2025-10-30T13:20:48+09:00'
id: be0a6e1cf2b4f42c2a70
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
GitHub Actionsで新しいLinuxランナー「`ubuntu-slim`」が公開プレビュー (Public Preview) になったというニュースを見かけたので、早速調べてみました！

https://github.blog/changelog/2025-10-28-1-vcpu-linux-runner-now-available-in-github-actions-in-public-preview/

`ubuntu-latest` よりもスペックを抑えた、低コストな選択肢のようです。

この記事では、従来の `ubuntu-latest` と何が違うのか、料金体系や向いているタスクについて、調べたことをまとめます。

:::note warn
プレビュー段階のため、正式リリース時には仕様変更の可能性がある点にご注意ください
:::


## `ubuntu-slim` とは？

`ubuntu-slim` は、GitHubホストランナーの新しいラインナップで、従来のスタンダードなLinuxランナー (`ubuntu-latest`) よりも軽量なスペック（1vCPU, 5GB RAM）が特徴です。

### スペック比較

`ubuntu-latest` と比較すると、以下のようになります。

| ランナー | vCPU | メモリ (RAM) | ストレージ (SSD) | 実行環境 |
| :--- | :---: | :---: | :---: | :--- |
| `ubuntu-latest` （パブリックリポジトリ） | 4 | 16 GB | 14 GB | VM |
| `ubuntu-latest`（プライベートリポジトリ） | 2 | 7 GB | 14 GB | VM |
| `ubuntu-slim` | 1 | 5 GB | 14 GB | コンテナ |

プライベートリポジトリの方が `ubuntu-latest` のスペックが低めで、`ubuntu-slim` との差が少ないため、使うメリットが大きそうです。

スペック以外の大きな違いとして、`ubuntu-slim` はVMではなく、コンテナ内でジョブを実行する点です。

また、`ubuntu-slim` には 1ジョブあたり15分 の実行時間制限があり、それ以上のジョブは失敗するとのことです。

### 料金と無料枠

一番気になるのがコスト面かと思います。

#### 有料プラン（従量課金）の料金

有料プランの分あたりの料金は、`ubuntu-latest` の1/4の価格に設定されています。

* `ubuntu-latest` (Linux, 2 vCPU): $0.008 / min
* `ubuntu-slim` (Linux, 1 vCPU): $0.002 / min

#### 無料枠の消費分（Minute Multipliers）

GitHub Actions の料金ドキュメント を確認したところ、無料枠の消費量を計算する「分倍率 (Minute multiplier)」は、OSごとに設定されています。

* Linux: 1x
* Windows: 2x
* macOS: 10x

現時点では、`ubuntu-slim` 用の特別な倍率（例えば 0.25x）が設定されているという情報は見つけられませんでした。
なので、`ubuntu-slim` を使った場合、「無料枠を消費する速度は `ubuntu-latest` と変わらないかもしれません。

https://docs.github.com/en/actions/reference/runners/github-hosted-runners

https://docs.github.com/en/billing/reference/actions-minute-multipliers

## どんなタスクに向いている？
公式のChangelog によると、`ubuntu-slim` は高負荷なCI/CDビルドには向いておらず、短時間のジョブに最適化されているとのことです。

### 例
* リント (Linting) やフォーマット (Formatting)
    * `eslint` や `prettier` のチェック
* イシューの自動ラベル付け
    * IssueOps関連のアクション実行
* 基本的なコンパイル
    * `webpack` でのビルドなど、比較的軽量なもの

GitHub Actions はジョブの実行時間がたとえ1秒でも「1分」としてカウントされる（切り上げ） ため、元々実行時間が数秒〜数分で終わるような軽いタスクに `ubuntu-slim` を使うと、ちりつもでの削減は期待できそうです。

## 設定方法

変更はとても簡単で、ワークフローファイルの `runs-on:` を変更するだけです。

```diff: .github/workflows/lint.yml
jobs:
  lint:
-   runs-on: ubuntu-latest
+   runs-on: ubuntu-slim
    steps:
```

## ワークフローのメトリクスを収集する

`ubuntu-slim` への移行を検討するにあたって、「どのワークフローがどれくらいのリソース（CPUやメモリ）を使っているのか分からない」というケースも多いかと思います。

そうした場合、[workflow-telemetry-action](https://github.com/catchpoint/workflow-telemetry-action
) を使うと、ワークフロー実行時のメトリクスを収集できます。

どのワークフローを `ubuntu-slim` に移行できそうか判断する材料集めとして便利かもしれません！

> ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/daeab0f7-381f-4597-9cf2-253d2e09abae.png)
https://github.com/catchpoint/workflow-telemetry-action


:::note warn
プロジェクトの要件やセキュリティポリシーに合っているか、事前にご確認いただくことをお勧めします
:::

## 最後に
ここまで読んでいただき、ありがとうございます！
高負荷なビルドは従来通り `ubuntu-latest` を使いつつ、lint や IssueOps などの軽いタスクは `ubuntu-slim` に寄せることで、特に有料プランを利用している場合はコストを最適化できそうですね！
