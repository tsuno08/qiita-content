---
title: Qiitaで既存記事の移行からGitHub管理まで爆速で終わらせるスクリプトを書いた
tags:
  - Qiita
  - GitHub
  - QiitaCLI
private: false
updated_at: '2025-11-21T22:21:31+09:00'
id: 90eedab306e502a06466
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
最近やっと重い腰を上げて [qiita-cli](https://github.com/increments/qiita-cli) を使い始めました。
ブラウザも悪くないんですが、やっぱり慣れたエディタを使いたいなと

これから導入する人に役立つかもと思い、環境構築から既存記事のダウンロード、リポジトリ作成、GitHub Secrets登録までを簡単にできるスクリプトを書いてみました！

::: note warn
トークンなどの取り扱いには注意してください
環境によって挙動が異なる場合があるため、中身を理解した上で実行してください
利用は自己責任でお願いします🙇‍♂️
:::

## 前提

これらが入っている環境で動きます。
  * Node.js (v18+)
  * Git
  * GitHub CLI (`gh`) ※ログイン済みであること
  * Qiita Access Token

## スクリプト

これを `setup_qiita.sh` とかで保存してください。

`gh` コマンドを使っているので、GitHub上でのリポジトリ作成やSecrets設定もするようにしています。

`.gitignore` やワークフローファイルも `qiita init` がよしなにやってくれるので、スクリプトはかなりシンプルです。

```bash:setup_qiita.sh
#!/bin/bash

read -p "GitHubのリポジトリ名を入力してください: " INPUT_NAME
PROJECT_NAME="${INPUT_NAME:-qiita-content}" # 好きなディレクトリ名・リポジトリ名に変えてください

mkdir "$PROJECT_NAME"
cd "$PROJECT_NAME"

# GitHubリポジトリ作成 (Public)
echo "GitHubリポジトリを作成しています..."
git init -q
gh repo create "$PROJECT_NAME" --public --source=. --remote=origin

# インストール
echo "qiita-cliをインストールしています..."
npm init -y > /dev/null
npm install --save-dev @qiita/qiita-cli > /dev/null

# 初期化
echo "qiita initを実行しています..."
npx qiita init

# Qiitaログイン (qiitaのアクセストークンでログイン)
npx qiita login

# 既存記事を全部持ってくる
echo "既存の記事を取得しています..."
npx qiita pull

# GitHub Secrets 登録
echo "GitHub Secretsを設定しています..."
read -p "Qiitaのアクセストークンを入力してください: " QIITA_TOKEN

if [ -n "$QIITA_TOKEN" ]; then
  gh secret set QIITA_TOKEN --body "$QIITA_TOKEN" --repo "https://github.com/$(gh api user --jq .login)/$PROJECT_NAME"
else
  echo "シークレットの設定をスキップしました。"
fi

# テスト記事作成
echo "テスト記事を作成しています..."
npx qiita new "test"

# プレビュー実行
echo "プレビューサーバーを起動しています..."
npx qiita preview

echo "新しく作成した差分をpushするとGitHub Actionsで自動デプロイされて記事が更新されます。"
```

## 使い方

先ほどのスクリプトをターミナルで叩くだけです。

```bash
sh ./setup_qiita.sh
```

実行中に

* GitHubのリポジトリ名（`PROJECT_NAME` 変数）
* Qiitaのアクセストークン

を聞かれるので、適宜入力してください。

最後まで行くとプレビューが立ち上がり、うまく表示されます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f228454a-33b6-4d08-8240-9ca46f27d1d9.png)

ディレクトリを見ると、既存の記事が `public/` 以下に全て `md` ファイルとして落ちてきています。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/d5062734-8110-4f3b-b48e-c991b1cdebe9.png)

あとは新規で記事を書く場合は好きなエディタで書いて `git push` するだけです。

pushしてGitHubを見に行くと、リポジトリが作られ、既に記事一式がPushされ、Actionsの設定も完了しているはずです。

![スクリーンショット 2025-11-21 22.18.20.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/b179c5ed-0fab-4ce1-8cfe-ab73e56541a1.png)


## おまけ
既存の記事を取得した際にファイル名が `[id].md` になるので、`[title].md` に変換するスクリプトも作りました！

```bash:rename.sh
#!/bin/bash

echo "リネーム処理を開始します..."

for file in public/*.md; do
  # titleを抽出
  title=$(grep -m 1 "^title:" "$file" | sed -E 's/^title:[[:space:]]*//;s/^["\x27]//;s/["\x27]$//;s/\r//g')

  if [ -n "$title" ]; then
    # ファイル名として安全な文字列に変換
    safe_title=$(echo "$title" | sed 's/\//／/g')
    new_filename="public/${safe_title}.md"
    # リネーム実行
    if [ "$file" != "$new_filename" ]; then
      if [ ! -f "$new_filename" ]; then
        mv "$file" "$new_filename"
        echo "✓ [OK] $file -> $new_filename"
      else
        echo "[SKIP] 移動先が存在するためスキップ: $new_filename"
      fi
    fi
  else
    echo "[ERROR] $file : titleが見つかりません"
  fi
done

echo "完了"
```

## おわりに
最後までお読みいただきありがとうございました！
Qiita CLI、めちゃくちゃ便利なのでぜひ試してみてください！

## 参考

https://qiita.com/Qiita/items/32c79014509987541130
