---
title: GitHub Copilot coding agentについて簡潔にまとめる
tags:
  - GitHub
  - AI
private: false
updated_at: '2025-05-20T22:30:53+09:00'
id: 674bee62471084ecec97
organization_url_name: null
slide: false
ignorePublish: false
---
先日リリースされたGitHub Copilot coding agentについて簡潔に書いてみました！
試行回数が少ないので間違っている部分もあるかもしれません🙇‍♂️

https://github.blog/changelog/2025-05-19-github-copilot-coding-agent-in-public-preview/

# Copilot
（日本語訳）
Copilot コーディング エージェントを使用すると、GitHub Copilot は人間の開発者と同じようにバックグラウンドで独立して動作し、タスクを完了できます。
> With Copilot coding agent, GitHub Copilot can work independently in the background to complete tasks, just like a human developer.

https://docs.github.com/en/copilot/using-github-copilot/using-copilot-coding-agent-to-work-on-tasks/about-assigning-tasks-to-copilot#overview-of-copilot-coding-agent


# 使用方法
前提として、GitHub Copilot Pro+ および GitHub Copilot Enterprise プランで利用可能です

- IssueにCopilotをアサインする
- https://github.com/copilot や GitHubの右上から開けるチャットでプルリクエストの作成を依頼

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8e6959a7-9eaa-47ba-8157-d0b21bb3d2be.png)


https://docs.github.com/en/copilot/using-github-copilot/using-copilot-coding-agent-to-work-on-tasks/about-assigning-tasks-to-copilot

# わかったこと
### 動作関連
- レビューコメントに反応する
  - 作ってもらったPRにコメントすると反応して修正してくれる
  - リプライにも反応
  - Copilotのレビューコメントには反応しない
    - Copilotのレビューのリプライにユーザーがコメントした場合も反応しない
  - まとめてコメントすると、コミットもまとめてくれる
- 良い結果を得るには
  - スコープが明確に設定されたタスクを割り当てる
- CopilotによるベースブランチやPRの文章の変更
  - PR作成後にはできない
  - 作成前ならChatの指示で出すことにより可能

### 設定関連
- MCPも設定可能
  - https://docs.github.com/en/enterprise-cloud@latest/copilot/customizing-copilot/extending-copilot-coding-agent-with-mcp
- 日本語対応
  - 日本語でレビューしてもらう方法と同様でPRのテンプレートを使う（以下が参考になります🙏）
    - https://zenn.dev/rescuenow/articles/55ea72023527d1
- カスタム指示
  - `.github/copilot-instructions.md` で設定
- Copilot用の事前インストール
  - `.github/workflows/copilot-setup-steps.yml` にワークフローファイルを作る
    - https://docs.github.com/en/copilot/customizing-copilot/customizing-the-development-environment-for-copilot-coding-agent#preinstalling-tools-or-dependencies-in-copilots-environment

### 料金関連
- GitHub ActionsとCopilotのプレミアムリクエストの料金が発生する
  - プレミアムリクエストは6/4から

# 感想
今のところかなり良さげです！
どれくらい複雑な実装までできるかは掴めていないので要検証
コーディングエージェント一気に出てきましたね…
