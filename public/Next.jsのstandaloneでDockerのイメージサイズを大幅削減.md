---
title: Next.jsのstandaloneでDockerのイメージサイズを大幅削減
tags:
  - Docker
  - React
  - Next.js
private: false
updated_at: '2023-07-23T10:52:09+09:00'
id: b3a4fd57d0413d5d3437
organization_url_name: null
slide: false
ignorePublish: false
---
Next.jsのstandaloneのビルドとDockerのマルチステージビルドを試してみたら良い感じだったので書いてみました！

# Next.jsのstandaloneのビルドとは？
アプリケーションをビルドするために必要なファイル（node_modulesも含め）を特定してビルドしてくれます。
→ ビルドのサイズを大幅に削減することができる！

使い方は簡単で、`next.config.js` の下記の部分に `output: 'standalone'` を追加するだけです。
```js:next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
/* ↓↓↓追加した部分↓↓↓ */
  output: 'standalone',
/* ↑↑↑追加した部分↑↑↑ */
}

module.exports = nextConfig
```

以前は、`next start` を実行するためにパッケージの依存関係からすべてのファイルをインストールする必要があったみたいです。

https://nextjs.org/docs/pages/api-reference/next-config-js/output

内部では `@vercel/nft` を使用して、import、require、およびfsの使用状況を静的に分析して、ページが読み込む可能性のあるファイルを決定しているみたいです。

↓より詳しく知りたい方は覗いてみてください！

https://github.com/vercel/nft

# マルチステージビルドについて
1つのDockerfile内で複数のステージを定義して、効率的なイメージを作成することができます！
→ 最終的なイメージには必要なファイルのみ含まれて、イメージサイズが小さくなる！

https://docs.docker.jp/develop/develop-images/multistage-build.html

マルチステージビルドが出る前は `Dockerfile` を複数作り、シェルスクリプトと組み合わせたりして、同じようなことをやっていたみたいです。

# 実際に書いてみる
実際にDockerfileを書いてみました。create-next-appで作ったものに追加しています。

## シンプルなDockerfileを作ってビルド

まず、特に工夫せずに書いたシンプルなDockerfileがこんな感じだと思います。

### npmの場合
```dockerfile:Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install

COPY . .

RUN npm run build

CMD [ "npm", "start" ] 
```

### yarnの場合
```dockerfile:Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package.json yarn.lock ./ 

RUN yarn install

COPY . .

RUN yarn build

CMD [ "yarn", "start" ] 
```

### ビルド
以下のコマンドでビルドします。
```bash
docker build -t before .
```

## standaloneやマルチステージビルドを使ったDockerfileを作ってビルド
次に、standaloneやマルチステージビルドを使ったDockerfileです。
※ `next.config.js`　に `output: 'standalone'` を追記するのを忘れずに

### npmの場合
```dockerfile:Dockerfile
# 外部イメージをbaseステージとして扱う
FROM node:18-alpine AS base

# baseステージをもとにbuilderステージを開始
FROM base AS builder

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install

COPY . .

RUN npm run build

# baseステージをもとにrunnerステージを開始
FROM base AS runner

WORKDIR /app

# public と .next/static は nextjs の standalone を使う場合に含まれないため、コピーする必要がある
# https://nextjs.org/docs/advanced-features/output-file-tracing#automatically-copying-traced-files
# builderから必要なファイルだけコピーする
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/static ./.next/static

COPY --from=builder /app/.next/standalone ./

# `next start` の代わりに `node server.js` を使用
# https://nextjs.org/docs/advanced-features/output-file-tracing#automatically-copying-traced-files
CMD ["node", "server.js"]
```

### yarnの場合
```dockerfile:Dockerfile
# 外部イメージをbaseステージとして扱う
FROM node:18-alpine AS base

# baseステージをもとにbuilderステージを開始
FROM base AS builder

WORKDIR /app

COPY package.json yarn.lock ./

RUN yarn install

COPY . .

RUN yarn build

# baseステージをもとにrunnerステージを開始
FROM base AS runner

WORKDIR /app

# public と .next/static は nextjs の standalone を使う場合に含まれないため、コピーする必要がある
# https://nextjs.org/docs/advanced-features/output-file-tracing#automatically-copying-traced-files
# builderから必要なファイルだけコピーする
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/static ./.next/static

COPY --from=builder /app/.next/standalone ./

# `next start` の代わりに `node server.js` を使用
# https://nextjs.org/docs/advanced-features/output-file-tracing#automatically-copying-traced-files
CMD ["node", "server.js"]
```

### ビルド
以下のコマンドでビルドします
```bash
docker build -t after .
```

# 結果
イメージサイズの結果はそれぞれ以下のようになりました！
※create-next-appで作ったもので比較しています🙇

```bash
docker images --format "{{.Repository}} {{.Size}}"
# after 204MB
# before 966MB
```

約1/5になった、すごい！！！
実際のプロジェクトでは正常に動作することを確認することをお勧めします🙇
（実際のプロジェクトだとどれくらい効果あるんだろう）

# 最後に
ここまで読んでくださりありがとうございます！
読んでくださった方の何かしらの役に立てれば嬉しいです！感想などいただけますと励みになります！

ちなみに Next.js の examples も standalone とマルチステージビルドを使って書かれてました！
`npx create-next-app --example with-docker <ディレクトリ名>` みたいな感じで簡単に使えますね！

https://github.com/vercel/next.js/blob/canary/examples/with-docker/Dockerfile

## 参考にしたもの

https://nextjs.org/docs/pages/api-reference/next-config-js/output

https://docs.docker.jp/develop/develop-images/multistage-build.html
