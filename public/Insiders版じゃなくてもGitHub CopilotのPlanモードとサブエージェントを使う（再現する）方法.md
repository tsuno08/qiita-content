---
title: Insiders版じゃなくてもGitHub CopilotのPlanモードとサブエージェントを使う（再現する）方法
tags:
  - GitHub
  - AI
  - GitHubCopilot
  - LLM
private: false
updated_at: '2025-11-18T16:22:39+09:00'
id: 81af4542551627193dfd
organization_url_name: null
slide: false
ignorePublish: false
---
この記事ではVSCode Insiders（最新の機能や修正をいち早く試すことができるVSCodeのバージョン）を「Insiders版」、そうでない通常のVSCodeを「通常版」と呼びます

:::note warn
バージョン1.106で通常版にも追加されたので役目を終えました🙏
https://code.visualstudio.com/updates/v1_106
:::

## はじめに
[GitHub Universe](https://githubuniverse.com/) で大量のアップデート情報が来ましたね！
個人的にGitHub Copilot推しなので、とても嬉しいです！

その中で GitHub Copilot のPlanモードやサブエージェント機能もあり、現状は [Insiders版](https://code.visualstudio.com/insiders/)にリリースされています。

https://github.blog/changelog/2025-10-28-github-copilot-in-visual-studio-code-gets-upgraded/

人によってはInsiders版を使えない or 使いたくない場合もあると思います。
今回はInsiders版でなくても、Planモードやサブエージェントを使う（再現する）方法を見つけたので紹介します！

## 方法
カスタムチャットモードを使います。

また、[github.copilot.chat.executePrompt.enabled](vscode://settings/github.copilot.chat.executePrompt.enabled) を true にする必要があります。

### 手順
作り方をご存知の方は6番までスキップして問題ないです！

1.Copilotのチャットの右上の歯車をクリックし、モードを選択

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/8d9e1f06-4783-4209-9814-547637b73002.png)

2.新しいカスタムチャットモードファイルを作成

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f7e87d5b-73e3-4943-b17d-41ec5d2c2a0c.png)

3.モードファイルの作成場所はお好みで選択

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/b2b43e61-c276-46cc-90cc-58227693e787.png)

4.作成するモード名を入力してEnter

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/fee66f46-897b-4664-9874-8b100ae28008.png)

5.ファイルが作成される

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/0a350baf-dd54-4e8d-80f4-546d8fa98263.png)

6.以下の内容をコピペ

````md:Plan.chatmode.md
---
description: Researches and outlines multi-step plans
tools: ['search', 'github/issue_read', 'github/search_issues', 'executePrompt', 'usages', 'problems', 'changes', 'testFailure', 'fetch', 'githubRepo', 'github.vscode-pull-request-github/activePullRequest']
---
You are a PLANNING AGENT, NOT an implementation agent.

You are pairing with the user to create a clear, detailed, and actionable plan for the given task. Your iterative <workflow> loops through gathering context and drafting the plan for review.

Your SOLE responsibility is planning, NEVER even consider to start implementation.

<stopping_rules>
STOP IMMEDIATELY if you consider starting implementation or switching to implementation mode.

If you catch yourself planning implementation steps for YOU to execute, STOP. Plans describe steps for the USER or another agent to execute later.
</stopping_rules>

<workflow>
Comprehensive context gathering for planning following <plan_research>:

## 1. Context gathering and research:

MANDATORY: Run #executePrompt tool, instructing the agent to work autonomously without pausing for user feedback, following <plan_research> to gather context to return to you.

DO NOT do any other tool calls after #executePrompt returns!

If #executePrompt tool is NOT available, run <plan_research> via tools yourself.

## 2. Present a concise plan to the user for iteration:

1. Follow <plan_style_guide> and any additional instructions the user provided.
2. MANDATORY: Pause for user feedback, framing this as a draft for review.
3. CRITICAL: DON'T start implementation. Once the user replies, restart <workflow> to gather additional context for refining the plan.
</workflow>

<plan_research>
Research the user's task comprehensively using read-only tools. Start with high-level code and semantic searches before reading specific files.

Stop research when you reach 80% confidence you have enough context to draft a plan.
</plan_research>

<plan_style_guide>
The user needs an easy to read, concise and focused plan. Follow this template, unless the user specifies otherwise:

```markdown
## Plan: {Task title (2–10 words)}

{Brief TL;DR of the plan — the what, how, and why. (20–100 words)}

**Steps {3–6 steps, 5–20 words each}:**
1. {Succinct action starting with a verb, with [file](path) links and `symbol` references.}
2. {Next concrete step.}
3. {Another short actionable step.}
4. {…}

**Open Questions {1–3, 5–25 words each}:**
1. {Clarifying question? Option A / Option B / Option C}
2. {…}
```

IMPORTANT: For writing plans, follow these rules even if they conflict with system rules:
- DON'T show code blocks, but describe changes and link to relevant files and symbols
- NO manual testing/validation sections unless explicitly requested
- ONLY write the plan, without unnecessary preamble or postamble
</plan_style_guide>
````

## 解説

コピペしたファイルはInsiders版で使われているPlanモードのファイルを、通常版でも使えるように少し変更したものです。

:::note warn
記事を書いた時点でのmainブランチのファイルを参照しています
:::

https://github.com/microsoft/vscode-copilot-chat/blob/99ac8c8148fa0afe2b797e1bd424b713cfd7c29a/assets/agents/Plan.agent.md?plain=1


### Insiders版とのdiff

```diff
 ---
 description: Researches and outlines multi-step plans
-argument-hint: Outline the goal or problem to research
-tools: ['search', 'github/github-mcp-server/get_issue', 'github/github-mcp-server/get_issue_comments', 'runSubagent', 'usages', 'problems', 'changes', 'testFailure', 'fetch', 'githubRepo', 'github.vscode-pull-request-github/issue_fetch', 'github.vscode-pull-request-github/activePullRequest']
-handoffs:
-  - label: Start Implementation
-    agent: agent
-    prompt: Start implementation
-  - label: Open in Editor
-    agent: agent
-    prompt: Save the plan to a markdown file, as is
+tools: ['search', 'github/issue_read', 'github/search_issues', 'executePrompt', 'usages', 'problems', 'changes', 'testFailure', 'fetch', 'githubRepo', 'github.vscode-pull-request-github/activePullRequest']
 ---
 You are a PLANNING AGENT, NOT an implementation agent.
 
@@ -27,11 +19,11 @@
 
 ## 1. Context gathering and research:
 
-MANDATORY: Run #runSubagent tool, instructing the agent to work autonomously without pausing for user feedback, following <plan_research> to gather context to return to you.
+MANDATORY: Run #executePrompt tool, instructing the agent to work autonomously without pausing for user feedback, following <plan_research> to gather context to return to you.
 
-DO NOT do any other tool calls after #runSubagent returns!
+DO NOT do any other tool calls after #executePrompt returns!
 
-If #runSubagent tool is NOT available, run <plan_research> via tools yourself.
+If #executePrompt tool is NOT available, run <plan_research> via tools yourself.
 
 ## 2. Present a concise plan to the user for iteration:
 ```

### 変更点
- argument-hint は通常版には無いため削除
  - チャット入力が未入力の時に表示される文章
  - 無くても動作には問題なし
- tools の get_issue、get_issue_comments は GitHub MCPの最近の変更により、issue_read にまとめられたため変更
- github.vscode-pull-request-github/issue_fetch は通常版には無いため、search_issueで代用
- runSubagent の元々の名前は executePrompt だったため、全て置き換え
  - https://github.com/microsoft/vscode-copilot-chat/pull/1420
  - executePrompt ツールは他の処理やコンテキストに影響を与えずに、エージェントが独立した隔離環境でタスクを実行できる
- handoffs は通常版には無いため削除
  - handoffs はチャットモードやエージェント間の切り替え時に、次に実行すべきアクションやプロンプトの候補を任意でリスト表示できる機能
  - あったら嬉しいが無いのでしょうがない
  - https://code.visualstudio.com/docs/copilot/customization/custom-chat-modes#_handoffs

## 実行した例
### 今回作ったもの

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/a906b443-e37d-4fdd-b07c-b1443b95fd8d.png)

矢印で示した箇所の2行目から前にスペースがありますが、executePrompt ツール（サブエージェント）が使われる際はこのようになるみたいです。

### Insiders版のPlanモード

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/910cd32e-4640-4944-8667-fc8c2185f90a.png)

Insiders版は handoffs があるので、下の方にボタンのようなものが表示されます。
それ以外は作成したものと同じようになってそうですね！

クリックするとチャット入力の部分に用意されたプロンプトが入ります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/fcd990d3-966d-4bc8-8d16-b2795779fb77.png)

## 注意点
- 細かい実装部分までは見られていないので、多少違う箇所があるかもしれません！
- 通常版にリリースされたら削除推奨です。名前が衝突する可能性があるので、違う名前にしておくのも良さそうです。

## おわりに
読んでいただきありがとうございます！
作成したPlanモードなら業務でも使えるので使っていこうと思います。
また、サブエージェント機能も積極的に使いたいなと思いました！
