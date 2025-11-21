---
title: 外部からGithub Actionsのワークフローを実行する（repository_dispatch）
tags:
  - GitHub
  - GitHubActions
private: false
updated_at: '2023-07-17T15:33:23+09:00'
id: 74125eb423cfbd3f1b6d
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
GitHub Actions の `repository_dispatch` を使用し、APIを介して外部からワークフローをトリガーする方法について紹介します。
最後まで読んでいただけると嬉しいです！


## `repository_dispatch`とは
`repository_dispatch` は Github Actions のワークフローをトリガーするイベントの一つです。
これを使うと、外部から Github Actions を実行することができます！
> GitHub の外部で生じるアクティビティのためにワークフローをトリガーしたい場合、GitHub API を使って、repository_dispatch と呼ばれる Webhook イベントをトリガーできます。

https://docs.github.com/ja/actions/using-workflows/events-that-trigger-workflows#repository_dispatch


## 実行されるワークフローの例
まず、実行される Github Actions のワークフローの例を紹介していきます。


以下はメッセージを `echo` コマンドで出力するワークフローです。

```yaml
name: Repository Dispatch Workflow
on:
  repository_dispatch:
    types: [echo_message]
jobs:
  echo_message:
    runs-on: ubuntu-latest
    steps:      
      - name: Echo Message
　　　　　　　　　　　　　　　　env:
          MESSAGE: ${{ github.event.client_payload.message }}
        run: echo ${{ env.MESSAGE }}
```

ここでポイントとなるのは以下の2点です。次の「APIを用いてトリガーする例」と照らし合わせてみるとわかりやすいです。
* types: [echo_message]
  * トリガーする側の `event_type` で `echo_message` が指定された場合に、このワークフローが実行される
  * （名前はなんでもいいが、トリガーする側で指定するものと同じにする必要がある）
* github.event.client_payload.message
  * トリガーする側の `client_payload` に渡された値を使用している

## APIを用いてトリガーする例
次に「実行されるワークフローの例」で記述したワークフローをトリガーする例です。

### curlを使った例
`curl` を使った例を紹介します。

https://curl.se/

```bash
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>"\
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/<OWNER>/<REPO>/dispatches \
  -d '{"event_type":"echo_message","client_payload":{"message":"Hello!"}}'
```

パラメータ|説明
---|---
YOUR-TOKEN|[個人用アクセストークン](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) を使用する
OWNER|ワークフローを実行するリポジトリのオーナーアカウント
REPO|リポジトリ名
event_type|repository_dispatchのtypesで指定されているものをトリガーする（100文字以下）
client_payload|オプションとして渡したい値を指定する（10個まで）

### JavaScriptを使った例
JavaScriptでは`Octokit.js`を使うと簡単に記述することができます。

https://github.com/octokit/octokit.js/

```javascript
// Octokit.js
// https://github.com/octokit/core.js#readme
const octokit = new Octokit({
  auth: <YOUR-TOKEN>
})

await octokit.rest.repos.createDispatchEvent({
  owner: <OWNER>,
  repo: <REPO>,
  event_type: 'echo_message',
  client_payload: {
    message: 'Hello!'
  },
})
```

それぞれのパラメータに関してはcurlのものと同様です！

### ワークフロー内でトリガーする例
もちろん、Github Actions のワークフロー内でも別のワークフローをトリガーするといったこともできます。

その際には、curlコマンドを記述したり、 `github-script` という便利なアクション（ライブラリみたいなもの）を使ってJavaScriptを記述できたりします。

https://github.com/actions/github-script

さらに `repository-dispatch` というアクションもあります。
名前からも予想付くと思いますが、`repository_dispatch` に特化していて記述をかなり楽にできます。

https://github.com/peter-evans/repository-dispatch

以下が `repository-dispatch` を使った例です。

```yaml
      - name: Repository Dispatch
        uses: peter-evans/repository-dispatch@v2
        with:
          token: <YOUR-TOKEN>
          repository: <REPO>
          event-type: echo_message
          client-payload: '{"message": "Hello!"}'
```

かなりシンプルになりました。
あと、紹介したアクションである `repository-dispatch` はかなりシンプルで自作のアクションを作る参考としてもかなり良さそうです！


## おわりに
最後まで読んでいただきありがとうございます！
`repository_dispatch` を使う機会はそんな多くないと思いますが、知っていて損はないと思います。
個人的に外部のシステムと連携する場合やワークフローの分離や共通化に便利そうだなと思いました！
もし、上手くハマった例とかあったらコメントしていただけると嬉しいです！
