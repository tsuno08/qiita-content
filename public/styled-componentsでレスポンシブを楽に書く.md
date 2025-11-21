---
title: styled-componentsでレスポンシブを楽に書く
tags:
  - CSS
  - JavaScript
  - TypeScript
  - React
  - styled-components
private: false
updated_at: '2020-11-13T07:34:28+09:00'
id: 9d2bae12ee8291ceeec8
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
今回はstyled-componentsを使ってレスポンシブデザインをできるだけ楽にする方法を紹介します。


## 実際に描いてみる
今回のは一回書いてしまえばつかいまわせるので簡単に書きます。

src直下にmedia.tsを作成し、以下のコードを書きます。Javascriptで書く場合はtypeの部分は全部消します。

```js:src/media.ts

import {
    css,
    CSSObject,
    FlattenSimpleInterpolation,
    SimpleInterpolation,
} from 'styled-components';

export const sp = (
    first: CSSObject | TemplateStringsArray,
    ...interpolations: SimpleInterpolation[]
): FlattenSimpleInterpolation => css`
    @media (max-width: 560px) {
        ${css(first, ...interpolations)}
    }
`;

export const tab = (
    first: CSSObject | TemplateStringsArray,
    ...interpolations: SimpleInterpolation[]
): FlattenSimpleInterpolation => css`
    @media (min-width: 561px) and (max-width: 1024px) {
        ${css(first, ...interpolations)}
    }
`;
export const pc = (
    first: CSSObject | TemplateStringsArray,
    ...interpolations: SimpleInterpolation[]
): FlattenSimpleInterpolation => css`
    @media (min-width: 1025px) {
        ${css(first, ...interpolations)}
    }
`;

```

styled-componentsからインポートしたcssは、関数みたいにも使えます。sp(スマートフォン)、tab(タブレット)、pc(パソコン)という関数を作って、それぞれのサイズでスタイルを当てたい時に呼び出して使います。

引数のタイプはVSCodeの型推論と同じになるようにしただけです。

次にApp.tsxで以下のコードを書いて、使ってみましょう。

```js:src/App.tsx

import React from 'react';
import styled from 'styled-components';
import { pc, sp, tab } from './media';

export const App = () => <Box>レスポンシブ</Box>;

const Box = styled.div`
    background-color: red;
    ${sp`
        width: 20px;
        height: 20px;
    `}
    ${tab`
        width: 50px;
        height: 50px;
    `}
    ${pc`
        width: 100px;
        height: 100px;
    `}
`;

```

ブラウザで確認するとそれぞれのサイズでちゃんと赤い正方形のサイズが変わると思います。
ブレイクポイントなど自由に変えて使ってみてください。

## 終わりに
ここまで読んで頂きありがとうございます！感想やリクエストなどどんどん送ってくれると嬉しいです！

## 参考記事
* [styled-componentsドキュメント](https://styled-components.com/docs/faqs#can-i-nest-rules)
* [【新定番】レスポンシブデザインのブレイクポイントの正解はこれだった[2019最新版]](https://hashimotosan.hatenablog.jp/entry/2019/05/28/164834)
