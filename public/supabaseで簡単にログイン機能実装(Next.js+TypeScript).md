---
title: supabaseで簡単にログイン機能実装(Next.js+TypeScript)
tags:
  - JavaScript
  - TypeScript
  - React
  - Next.js
  - Supabase
private: false
updated_at: '2021-01-24T17:28:04+09:00'
id: e730d037b679890e3d02
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
最近、supabaseという便利なものを見つけたので少し使ってみました！今回は認証だけ使っています。

今回のコード↓
https://github.com/NozomuTsuruta/next-supabase-auth-example

参考：https://app.supabase.io/

## supabaseとは
Firebaseのようにデータベースや認証を簡単に使えるサービスです。データベースはFirebaseはNoSQLなのに対し、supabaseはPostgreSQLが採用されています。個人的に新しく覚えることが少なくてわかりやすいです。比較的新しいのでFirebaseほどたくさんの機能はありませんが、今後どんどん追加されていくみたいです。

## 導入
まずはインストールします。

```zsh
## npm
npm install @supabase/supabase-js

## yarn
yarn add @supabase/supabase-js
```

[https://app.supabase.io/](https://app.supabase.io/)にアクセスして、アカウントを作成し、新しいプロジェクトを作成します。
作成した後、そのプロジェクトを開き、SettingsのAPIのページに行くと、URLとAPI_KEYS(public)があると思います。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/defdb7fd-8684-6012-15f3-68ff583d7ad5.png)

それをコピーして、`next.config.js`に追加します。

```js:next.config.js
module.exports = {
  env: {
    SUPABASE_URL: "[URL]",
    SUPABASE_KEY: "[PUBLIC_API_KEY]",
  },
};

```




## 実際に使ってみる
ディレクトリ構成↓
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/d2f496bf-c0cc-ae07-bc41-58016e3b513a.png)

### supabaseの設定ファイル
下のように先ほど`next.config.js`に追加した値をここで使います。

```ts:src/util/supabase.ts
import { createClient } from "@supabase/supabase-js";

export const supabase = createClient(
  process.env.SUPABASE_URL as string,
  process.env.SUPABASE_KEY as string
);
```

あとは、`src/util/supabase.ts`から`supabase`をインポートして簡単に使えます。

### Formコンポーネント
いろんなページで使う用に作ります。

```tsx:src/components/Form.tsx
import { FC } from "react";

type IProps = {
  onSubmit: any;
  buttonText: string;
  inputList: {
    name: string;
    ref: any;
    type: string;
  }[];
};

export const Form: FC<IProps> = ({ onSubmit, buttonText, inputList }) => {
  return (
    <form onSubmit={onSubmit}>
      {inputList.map((props) => (
        <label key={props.name}>
          <span>{props.name}</span>
          <input {...props} />
        </label>
      ))}
      <button type="submit">{buttonText}</button>
    </form>
  );
};
```

それぞれのページを簡単に作っていきます。react-hook-formを使っているので使い方がわからない方はみてみてください！
[react-hook-formの使い方を解説 v6.13.0 useController追加!](https://qiita.com/NozomuTsuruta/items/0140acaee87b7c4ed856)

### サインインページ

```tsx:pages/signin.tsx
import { NextPage } from "next";
import { useForm } from "react-hook-form";
import { Form } from "../components/Form";
import { supabase } from "../util/supabase";

type IForm = {
  email: string;
  password: string;
};

const Signin: NextPage = () => {
  const { register, handleSubmit } = useForm<IForm>();
  const handleSignin = ({ email, password }: IForm) => {
    supabase.auth.signIn({ email, password });
  };

  const inputList = [
    { type: "email", name: "email", ref: register },
    { type: "password", name: "password", ref: register },
  ];

  return (
    <Form
      onSubmit={handleSubmit(handleSignin)}
      inputList={inputList}
      buttonText="サインイン"
    />
  );
};

export default Signin;
```

### サインアップページ
サインアップ後にそのまま登録するか、メールアドレス確認するかどうかはsupabaseで設定できます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/f40203f8-5e19-bcc9-a640-8e45217b5499.png)


```tsx:src/pages/signup.tsx
import { NextPage } from "next";
import { useForm } from "react-hook-form";
import { Form } from "../components/Form";
import { supabase } from "../util/supabase";

type IForm = {
  email: string;
  password: string;
  passwordConf: string;
};

const Signup: NextPage = () => {
  const { register, handleSubmit } = useForm<IForm>();
  const handleSignup = ({ email, password }: IForm) => {
    supabase.auth.signUp({ email, password });
  };

  const inputList = [
    { type: "email", name: "email", ref: register },
    { type: "password", name: "password", ref: register },
    { type: "password", name: "passwordConf", ref: register },
  ];

  return (
    <Form
      onSubmit={handleSubmit(handleSignup)}
      inputList={inputList}
      buttonText="サインアップ"
    />
  );
};

export default Signup;
```

### パスワード再設定ページ
`resetPasswordForEmail`の引数にとったメールアドレスに再設定用のメールが送信されます。

```tsx:src/pages/forgot.tsx
import { NextPage } from "next";
import { useForm } from "react-hook-form";
import { Form } from "../components/Form";
import { supabase } from "../util/supabase";

type IForm = {
  email: string;
};

const Forgot: NextPage = () => {
  const { register, handleSubmit } = useForm<IForm>();

  const handleResetPassword = ({ email }: IForm) => {
    supabase.auth.api.resetPasswordForEmail(email);
  };

  const inputList = [{ type: "email", name: "email", ref: register }];

  return (
    <Form
      onSubmit={handleSubmit(handleResetPassword)}
      inputList={inputList}
      buttonText="パスワード再設定メール送信"
    />
  );
};

export default Forgot;
```

### ログイン後のページ

```tsx:src/pages/index.tsx
import { NextPage } from "next";

const Home: NextPage = () => {
  return <h1>Hello</h1>;
};

export default Home;
```

### _app.tsx
`onAuthStateChange`でログイン状態を監視できます。簡単にルーティングも実装しました。

```tsx:src/pages/_app.tsx
import { AppProps } from "next/app";
import { supabase } from "../util/supabase";
import { useRouter } from "next/router";
import { useEffect, useState } from "react";

export default function App({ Component, pageProps }: AppProps) {
  const { pathname, push } = useRouter();
  const [loading, setLoading] = useState(true);

  supabase.auth.onAuthStateChange((_, session) => {
    if (session?.user && (pathname === "/signin" || pathname === "/signup")) {
      push("/");
    } else if (!session?.user && pathname !== "/signup") {
      push("/signin");
    }
  });

  useEffect(() => {
    (async () => {
      const user = supabase.auth.user();
      if (user && (pathname === "/signin" || pathname === "/signup")) {
        await push("/");
      } else if (!user && pathname !== "/signup") {
        await push("/signin");
      }
      setLoading(false);
    })();
  }, []);

  return (
    <>
      {loading ? (
        <h1>loading...</h1>
      ) : (
        <>
          <button onClick={() => supabase.auth.signOut()}>ログアウト</button>
          <Component {...pageProps} />
        </>
      )}
    </>
  );
}
```

今回のコード↓
https://github.com/NozomuTsuruta/next-supabase-auth-example

## 終わりに
ここまで読んでいただきありがとうございます！今回CSSを書かなかったので見た目はだいぶ酷いと思います🙇‍♂️
今回データベースの方は使いませんでしたが、とても便利なので使ってみてください！



