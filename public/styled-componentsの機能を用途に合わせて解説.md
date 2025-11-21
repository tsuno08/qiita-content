---
title: styled-componentsの機能を用途に合わせて解説
tags:
  - CSS
  - JavaScript
  - TypeScript
  - React
  - styled-components
private: false
updated_at: '2020-11-25T12:36:47+09:00'
id: 1102ddc06dbfa15921e8
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
前回のreact-hook-formに続き、今回もreactの便利なパッケージのstyled-componentsについてまとめてみました！
自分もよく使うので使うときの参考にしたいと思ってます。

参考：[styled-componentsドキュメント](https://styled-components.com/docs/api)

## styled-componentsとは
ReactにおけるCSSの当て方の1つで、Reactは他にもCSSModulesやCSSinJSなど色々なCSSの当て方がありますが、その中でも人気があり、個人的に一番気に入っています。特徴として、スタイル付きのコンポーネントを作ることができ、それに値を渡すことができます。

## インストール
今回はTypeScriptを使うので、型定義ファイルもインストールします。

```zsh
## npm
npm install --save-dev styled-components @types/styled-components

## yarn
yarn add -D styled-components @types/styled-components
```

## 通常のCSSと同じような使い方

まずstyled-componentsから、styledをインポートします。
基本的な使い方として、`styled`の後にタグ名を書いて、その中で通常のscssのような感じで記述できます。

下の例では、h1タグにスタイルが当たった物をTitleとして使用しています。
その中のspanにもスタイルを当てたり、擬似要素を使ったりしています。

```react
import React from "react";
import styled from "styled-components";

export const App = () => {
  return (
    <Title>
      hello<span>world!</span>
    </Title>
  );
};

const Title = styled.h1`
  font-weight: bold;
  span {
    color: red;
  }
  &:hover {
    color: blue;
  }
`;
```

通常|hover
---|---
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/67550146-5aa5-adec-3a85-9981e166d93f.png">|<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/6f94fa21-4da8-e2e7-1821-4bdaff5c046e.png">


## コンポーネントのスタイルを拡張

自分の作成したコンポーネントやUIライブラリのコンポーネントにスタイルを当てたい時は、コンポーネント名を`()`で囲うように記述します。
以下の例では、上で作成したTitleを拡張しているので`font-weight: bold`になっていて、colorはgreenに上書きされています。

```react
const StyledTitle = styled(Title)`
    color: green;
`;
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/ddc0365a-085d-e815-6f49-a6462f860ac8.png)


UIライブラリによって、スタイルの優先度が高かったりして、思った通りにスタイルが当たらないことがあります。
（ドキュメントに載ってたり、調べたら大体すぐ出てくるので調べてみてください。）

## 他のタグでも使用する

上の方法だけだと、Titleのスタイルがh1タグとしてしか使えないので、divタグやpタグなど他のタグとしても使いたい時は、`as`を使います。
以下の例では作成したTitleをh1タグではなく、divタグとして使用しています。

```react

    <Title as="div">
      hello<span>world!</span>
    </Title>
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/e1cecba5-3859-bd08-a0fe-27502a07f930.png)

同じような機能として、`withComponent`というのがありますが、`as`の方が簡潔だからか非推奨になっているみたいです。

## 一部のスタイルを使いまわしたい
コンポーネントとしてではなく、スタイルを色々なところで使いたい時は`css`を名前インポートして使います。

下のような感じで、`css`を使ってスタイルを作成し、他のスタイルに埋め込むことができます。また、変数や定数を使うこともでき、スタイルを統一するのに役立ちます。


```react
const justifyContentCenter = css`
  display: flex;
  justify-content: center;
`;

const Container = styled.div`
  background-color: ${COLOR.WHITE};
  ${justifyContentCenter}
`;

const Box = styled.div`
    margin: 20px;
    ${justifyContentCenter}
`;
```

## １、２個スタイルを当てる時にわざわざコンポーネントを作りたくない
`cssprop`というのがあり、下のように記述できるのですが導入がやや面倒です。とても長くなってしまうので導入方法は割愛します。csspropはbabelを使って、classを作ってくれるのでインラインスタイルの優先度の問題は解決できますが、jsx内に多く記述すると見辛くなるので、１、２個程度で使う感じになると思います。

こちらの記事がとても参考になります！
[styled-componentsのcss propを、create-react-appで作成したReact & TypeScriptの環境で動かすためのチュートリアル](https://qiita.com/jonakp/items/cab93fbe28aeb922597d)

```react
    <h1 css={`color: white`}>
      hello world!
    </h1>
```

## 値を渡して使う

styled-componentsと言ったらこれ！みたいな感じがあります。下のように親コンポーネントから子コンポーネントにpropsを渡すのと同じようにして、値を渡すことができます。

```react
import React from "react";
import styled from "styled-components";

export const App = () => {
  return (
    <Title color="red">
      hello<span>world!</span>
    </Title>
  );
};

const Title = styled.h1<{ color: string }>`
  color: ${(props) => props.color};
`;

```

この例の場合、color='red'が渡されて、その値を下で参照しているので`color: red`になります。

複数の値を渡したり、分割代入で記述を減らしたりもできます

```react
import React from "react";
import styled from "styled-components";

export const App = () => {
  return (
    <Title color="red" background="black">
      hello<span>world!</span>
    </Title>
  );
};

const Title = styled.h1<{ color: string; background: string }>`
  color: ${({ color }) => color};
  background-color: ${({ background }) => background};
`;

```


## 終わりに
ここまで読んでいただきありがとうございました！少しでもstyled-componentsについて、理解が深まれば嬉しいです。
他にも`attrs`や`theme`などの機能がありますが、今のところあまり使用していない＆有効的な使い方がわかっていないので今回は書きませんでした。興味がある方は調べてみてください。もし良い使い方があったりしたら教えていただけると嬉しいです。
他にも記事投稿しているので良ければ見てください！
