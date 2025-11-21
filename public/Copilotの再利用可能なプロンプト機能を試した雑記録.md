---
title: Copilotの再利用可能なプロンプト機能を試した雑記録
tags:
  - Git
  - AI
  - MCP
  - LLM
private: false
updated_at: '2025-05-14T21:10:21+09:00'
id: 5224fb1ebcec69c73868
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

mainブランチでブランチを切らずに作業していた時にブランチ作成→コミットまでをAIで良い感じに出来そうだなと思って試した雑記事です🙇‍♂️

VSCodeのバージョン1.100で変わった再利用可能なプロンプト機能（機能名間違っているかも）を使いました

- https://code.visualstudio.com/docs/copilot/copilot-customization#_prompt-file-structure

## 再利用可能なプロンプト機能とは

少し前からVSCodeのGitHub Copilotで追加されていた機能です

元々はワークスペースの `.github/prompts` ディレクトリに `*.prompt.md` ファイルを置くと、Copilot Chatのコンテキストとして選択できるみたいな機能でした

（プロンプトファイルを置く場所は chat.promptFilesLocations で指定可能）

それがVSCodeのバージョン1.100から大幅に変わりました

実行可能なツールやモードを指定できたり、添付画像のような実行ボタンが追加されたりなど

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/58bb7adb-a20d-40c8-8c47-09eab9ed2ab2.png)


## 実際に試した例

以下のように使用するモードや実行可能なツール、やらせたいタスクを記述します

ちなみに⇧⌘P → Chat: New Prompt File を実行するとファイルを作るのが楽になります

- Git MCPを使っています
    - https://github.com/modelcontextprotocol/servers/tree/main/src/git
    - 今回のケースでは使わないであろうツール（git_reset、git_init）は省いています
- （プロンプトは雰囲気）

```markdown
---
mode: 'agent'
tools: ['git_status', 'git_diff_unstaged', 'git_diff_staged', 'git_diff', 'git_commit', 'git_add', 'git_log', 'git_create_branch', 'git_checkout', 'git_show']
description: 'ブランチを作成し、チェックアウトし、差分をコミットします。'
---

あなたはGitの扱いに長けたエンジニアです。以下のタスクを実行してください。指示を仰がずに、すべてのステップを自動的に実行してください。

## タスク
1. 現在の差分に基づいてブランチ名を提案し、そのブランチを作成します。
2. 作成したブランチにチェックアウトします。
3. 現在の差分を `git add` します。
4. 現在の差分に基づいて、日本語のコミットメッセージを提案し、`git commit` します。
```

▶️ボタンをクリックしたらCopilotが動いてチャットに反映されていきます

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/9119604a-42df-44cb-8e74-2b2771de3781.png)


## 感想

簡単な例ですが思い通りに行って嬉しかったのと、しっかり作ればかなり便利に使えそうだなと思いました！

PATをあまり発行したくなくて、GitHub MCPは使わなかったですが、一緒に使えば PR作成までできて便利だろうなと思います

少しでも参考になれば幸いです！
