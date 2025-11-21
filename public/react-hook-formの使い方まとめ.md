---
title: react-hook-formの使い方まとめ
tags:
  - JavaScript
  - TypeScript
  - React
  - react-hook-form
private: false
updated_at: '2024-04-08T22:03:50+09:00'
id: 60d15d97eeef71993f06
organization_url_name: null
slide: false
ignorePublish: false
---
久しぶりの投稿になります。今後は月1ぐらいで記事投稿できるようにに頑張ります！以前react-hook-formについての記事を書いたのですが、バージョン7になって大きく変わったので改めて書いてみました。バージョン7で大きく変わったので、バージョン7より前を使っている方も多いと思い、以前書いた記事も残しす形にしました。

バージョン6はこちら↓
[react-hook-formの使い方を解説 v6.13.0 useController追加!](https://qiita.com/NozomuTsuruta/items/0140acaee87b7c4ed856)

参考：[公式ドキュメント](https://react-hook-form.com/)

## react-hook-formとは
inputとかのformに関係するデータを使う際に、useStateを使うときよりもレンダリング回数を減らせたり、バリデーションも簡単に実装できてとても便利です！

## インストール

```zsh
## yarnの場合
yarn add react-hook-form

## npmの場合
npm install react-hook-form
```

以上！TypeScriptの型定義も含まれてます。

↓↓↓簡単な例

```tsx
import { useForm, SubmitHandler } from 'react-hook-form';
import { ErrorMessage } from '@hookform/error-message';

type Inputs = {
  name: string;
  email: string;
};

export const Demo = () => {
  const {
    register,
    handleSubmit,
    reset,
    formState: { errors },
  } = useForm<Inputs>();

  const onSubmit: SubmitHandler<Inputs> = (data) => {
    console.log(data);
    reset();
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      <input
        {...register('email', {
          required: true,
          maxLength: 60,
          pattern: {
            value:
              /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/,
            message: 'メールアドレスの形式が不正です',
          },
        })}
      />
      <ErrorMessage errors={errors} name="email" />
      <button type="submit">Submit</button>
    </form>
  );
};

```

`register`の記述が大きく変わり、以前name属性で書いていた部分を第一引数で書くようになり、入力補完も効くようになりました！`register`を使うときはref属性に書くのではなく、スプレッド構文を使うようになりました。
他にも`errors`がformStateの中に入ったりしています。


↓この例だと入力した値はこんな風に出力されます。それぞれの機能を解説して行きます。

```json
{name: 入力した値,email: 入力した値}
```

## useForm
react-hook-formからインポートするものはいくつかあるが、基本的にはほとんどuseFormから取得することが多い。


```react
const { register, reset, handleSubmit } = useForm({
  mode: onSubmit,
  defaultValues: {name: "aaa", email: test@test.com}
})
```

引数にオブジェクトで色んな設定みたいなのができます

|名前|説明|型|
|---|---|---|
|mode|初回のバリデーションの実行タイミング<br>(初期値：onSubmit)|onChange or onBlur or onSubmit or onTouched or all|
|reValidateMode|初回のバリデーション実行後、次のバリデーション実行タイミング<br>(初期値： onChange)|onChange or onBlur or onSubmit|
|defaultValues|初期値をオブジェクトで入力します<br>(resetでdefaultValuesの値に戻る)|{name属性の値: 初期値,...}|
|resolver|他のvalidationライブラリを使う時に使用||
|context|基本的には他のvalidationライブラリを使う時に使用？||
|criteriaMode|バリデーション時に最初に発生したエラーもしくは全てのエラーを収集<br>(初期値: firstError)|firstError or all|
|shouldUnregister|入力要素がアンマウントされたら、値の参照を解除、falseなら入力された値を保持<br>(初期値： false)|boolean|
|shouldFocusError|フォームが送信されて、エラーが含まれているときにエラーのある最初のフィールドにフォーカスする<br>(初期値： true)|boolean|
|delayError|エラー表示をミリ秒単位で遅らせる|number|
|shouldUseNativeValidation|ブラウザの元々のバリデーションを有効にする<br>(初期値： false)|boolean|

### register

inputなどに入力された値を参照するために使う。name属性を設定する必要があり、registerはrefに入れる。UIライブラリを使う場合は`control`参照。一番使う。

↓第一引数の付け方によって、dataの形が変わる.バージョン7以前と指定方法が変わりました。tsの場合は型も変える必要あり


>register("firstName")　→　	{firstName: 'value'}
register("name.firstName")　→ 　{name: { firstName: 'value' }}
register("name.firstName.0")　→　 {name: { firstName: [ 'value' ] }}



引数にオブジェクトで色んなバリデーションが設定できる.ref属性とname属性を使わず、第一引数に元々のnameを指定するようになった。

|名前|説明|型|
|---|---|---|
|required|必須項目にするか|true or false or エラーメッセージ|
|maxLength|最大文字数|{value: number, message: エラーメッセージ}|
|minLength|最小文字数|{value: number, message: エラーメッセージ}|
|max|最大データ量|{value: number, message: エラーメッセージ}|
|min|最小データ量|{value: number, message: エラーメッセージ}|
|pattern|文字の形式|{value: 正規表現, message: エラーメッセージ}|
|validate|カスタム|{任意の名前: value => booleanを返す式 or 関数 &#124;&#124; エラーメッセージ} |
|valueAsNumber|数値を返す、問題があればNaNを返す|boolean|
|valueAsDate|日付を返す、問題があればnullを返す|boolean|
|setValueAs|関数に値を通す|例）setValueAs:(value) => trim(value)|
|disabled|入力された値が無効になる<br>(初期値: false)|boolean|
|onChange|onChangeイベント時に実行される関数||
|onBlur|onBlurイベント時に実行される関数||
|value|入力の値|string, numberなど|
|shouldUnregister|アンマウント後に参照が解除され、初期値も削除される<br>(初期値: false)|boolean|



```react
    <input
        {...register('email', {
          required: true,
          maxLength: 60,
          pattern: {
            value:
              /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/,
            message: 'メールアドレスの形式が不正です',
          },
          validate: {
            lessThanTen: (value) =>
              parseInt(value, 60) < 60 || '60文字以内で入力して下さい',
          },
        })}
      />
```

### unregister
引数に入れたnameはregisterで参照しなくなる。配列を入れて、複数も可能。`unregister(['name','email'])`

```react
<button onClick={() => unregister("email")}>email_unregister</button>
```

### watch
第一引数入れたnameの値を監視。第二引数は初期値を設定可能。unregisterと同様に配列に入れて複数も可能。何も入れないと、全てのnameの値を取得。レンダリング回数は増える。また、元々はオブジェクトで返されていたが、バージョン7で配列として返されるようになった。

```react
const watchName = watch("name", false);
const watchAllFields = watch();
const watchFields = watch(["name", "email"]);
```

### handleSubmit
ラップした関数にformのdataをオブジェクトの形で渡してくれる。`on_submit`の引数にdataがあるのはそのため。
`event.preventDefalut()`も必要なし。

```react
import { useForm, SubmitHandler } from 'react-hook-form';

///

const onSubmit: SubmitHandler<Inputs> = (data) => {
    console.log(data);
};

return (
    <form onSubmit={handleSubmit(onSubmit)}>
...
```

###reset

formの中の状態を初期化する関数で指定した値を初期化し、指定なしなら全ての値。
第二引数でリセット時の設定が可能。


```react
const onClick =()=>{
  reset(values);
}
```

### setError
第一引数に指定したnameにエラーをセットする関数。第二引数にtype(requiredやminLengthなど)とエラーメッセージをオブジェクトとしていれる。
typeを変えるとどう変わるかが、よく分からないので分かる方いたら教えて下さい！

```react
<button onClick={() => setError("email", { type: "required", message: "" })}>
```

###clearErrors
引数に入れたnameのerrorを消す関数。こちらも配列で複数指定できる。

```react
<button onClick={() => clearErrors("name")}>
```

### getValues
引数に入れたnameの現在の値を取得。こちらも配列に入れて複数、空なら全てのnameの値を取得。watchとの違いは常に監視せず、再レンダリングもされない。ボタンを押して時に取得するとかならこっちの方がパフォーマンス的に良さそう。

```react
const values = getValues();
const singleValue = getValues("name");
const multipleValues = getValues(["name", "email"]);
```

### setValue
第一引数に入れたnameに第二引数に入れた値をセットする関数。その際に`shouldValidate`でバリデーションを実行するかどうか、`shouldDirty`で変更後に変更の有無の判定を行うかどうか

```react
<button onClick={()=> setValue('name', 'value', { shouldValidate: true, shouldDirty: true })}>Set</button>
```

### setFocus
第一引数に入れたnameに第二引数に入れた値をセットする関数。その際に`shouldValidate`でバリデーションを実行するかどうか、`shouldDirty`で変更後に変更の有無の判定を行うかどうか

```react
<button onClick={()=> setFocya('name')}>Focus</button>
```


### trigger
引数に入れたnameのバリデーションを実行する関数。こちらも配列で複数、空なら全体。元々はbooleanを返していたが、バージョン7で何も返さなくなった。


```react
<button type="button" onClick={() => { trigger("lastName") }}>Trigger</button>
```
### control
`Controller`のnameの値を参照するのに使う。バリデーションはできない。`useWatch`、`useFieldArray`にも使う。

```react
<Controller
  as={<TextInput />}
  control={control}
  name="name"
/>
```

### formState
form内の入力の有無や送信の状態などを取得できる。`formState.isSubmitting`はbuttonにローディングをつける時によく使う。バージョン7で`touched`が`touchedFields`に改名されました。

|名前|説明|型|
|---|---|---|
|isDirty|全体で何かしら変更があったらtrueになる|boolean|
|dirtyFields|それぞれの入力要素で何かしらの変更があったらtrue|{name属性の値: boolean,...}|
|touchedFields|それぞれの入力要素で何かしらの操作をしたらtrueになる<br>（フォーカスするだけでも、onBlurのタイミングで反映される）|{name属性の値: boolean,...}|
| isSubmitted |formが送信されたらtrue、resetでfalseに戻る|boolean|
| isSubmitSuccessful |formの送信が成功したらtrue|boolean|
|isSubmitting|formの送信中にtrue|boolean|
| submitCount |formの送信された回数|number|
|isValid|何かしらエラーがあったらtrue<br>(modeがonChange or onBlurの時のみ)|boolean|
|isValidating|バリデーション中かどうか？|boolean|
|errors|バリデーションのエラーのオブジェクトが入る|{name属性の値: {<br>type: "required",<br>message: "",<br>ref: \<input name="name属性の値" type="text"></input>,<br>},...}|

```react
import { useForm } from "react-hook-form";

export const Demo = () => {
  const { register, handleSubmit, errors, formState } = useForm();
  const onSubmit = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input name="name" ref={register} />
      {isDirty && <p>Form is dirty.</p>}
      {isSubmitting && <span>Submitting...</span>}
      <button type="submit" />
    </form>
  );
}
```

## Controller
UIライブラリを使う時に使用。`register`だと参照できないことが多い。(MaterialUIの`TextField`とかは`inputRef`に`register`を入れて参照できる)。`rule`で`register`の中に書く感じでバリデーションを書ける。`defaultValue`も設定可能
asの中に全部入れちゃうのが簡単

```react
<Controller
  as={<TextInput />}
  control={control}
  name="name"
  rules={{required: true}}
  defaultValue="aaa"
/>

<Controller
  control={control}
  name="email"
  render={({
    field: { onChange, onBlur, value, name, ref },
    fieldState: { invalid, isTouched, isDirty, error },
  }) => (
    <TextField
      onChange={onChange}
      inputRef={ref}
　　　　　　　　　　　　value={value}
    />
  )}
/>

```

## ErrorMessage

指定したnameのエラーメッセージを表示できる。messageはstringならOK


```react
import { ErrorMessage } from '@hookform/error-message';

<ErrorMessage errors={errors} name="email" message={errors.email?.message} />
```

## useFormContext, FormProvider
`FormProvider`に`useForm`の`register`や`errors`などをまとめて渡して、ラップされたコンポーネントで`useFormContext`を使うと、その渡された値を使うことができる。

```react
import { useForm, FormProvider, useFormContext } from "react-hook-form";

export const Demo = () => {
  const methods = useForm();
  const on_submit = data => console.log(data);

  return (
    <FormProvider {...methods} >
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <Input />
        <button type="submit" />
      </form>
    </FormProvider>
  );
}

const Input = () => {
  const { register } = useFormContext();
  return <input {...register('name')} />;
}

```
## useWatch
controlを渡すことで`watch`を別のコンポーネントでも使えるようにした感じ。

```react
import { useForm, useWatch } from "react-hook-form";

const Watch = ({ control }) => {
  const name = useWatch({ control, name: 'name', defaultValue: 'aaa' });

  return <div>{name}</div>;
}

type FieldInputs = {
    name: string;
}

export const Demo = () => {
  const { register, control, handleSubmit } = useForm<FieldInputs>();
  
  return (
    <form onSubmit={handleSubmit(data => console.log("data", data))}>
      <input ref={register} name="name" />
      <Watch control={control} />
      <button type="submit" />
    </form>
  );
}
```

## useFieldArray
下の写真みたいな感じで、同じinputなどを増減させたい時に使えます。公式ドキュメントから、YoutubeのuseFieldArryの紹介動画に飛べるので見るとわかりやすいです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/e22778dd-3709-e6e3-2e66-cf4d6dda692d.png)


↓こんな感じでuseFormのように色んなメソッドを使えます。nameとcontrolが必要です。defaultValueはuseFormで設定できます。

```react
const { fields, append, prepend, remove, swap, move, insert } = useFieldArray({
    control,
    name: "demo"
});
```
↓３列目は例です。

||||
|---|---|---|
|fields|mapを使ったりして、入力要素をレンダリングするのに使用。idやdefaultValueが入ったオブジェクト||
|append|fieldsの最後にinputを追加|append({name: "aaa"})|
|prepend|fieldsの先頭にinputを追加|prepend({name: "aaa"})|
|insert|fieldsの特定の位置にinputを追加|insert(3,{name: "aaa"})|
|swap|inputの位置を入れ替える|swap(1,2)|
|move|inputの位置を動かす|move(1,2)|
|replace|fieldsの値を置き換える|replace({name: 'aaa'})|
|remove|特定のinputを削除、引数無しで全削除|remove(1) remove()|


```react
import { SubmitHandler, useForm, useFormState } from 'react-hook-form';

type Inputs = {
  firstName: string;
  lastName: string;
};

export default function Demo() {
  const { register, handleSubmit, control } = useForm<Inputs>({
    defaultValues: {
      firstName: 'firstName',
    },
  });
  const { dirtyFields } = useFormState({
    control,
    name: 'firstName',
  });
  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} />
      {dirtyFields.firstName && <p>Field is dirty.</p>}
      <input {...register('lastName')} />
      <input type="submit" />
    </form>
  );
}



```

## useController
controllerと同じ感じで、nameとcontrolを渡してあげれば、UIコンポーネントの入力も取得できるようになり、rulesでバリデーションを記述できます。
他にも、meta内にisTouched、isDirty、invalidがあり、それぞれ、１回でも要素にタッチ（フォーカス）したか、値が入力の有無、エラーの有無がbooleanで入っているみたいです。

Controllerタグを使わなくていいのは機能とUI部分が分離できて嬉しいですね！

```tsx
import React,{ FC } from 'react';
import { TextField } from '@material-ui/core';
import { useForm, useController, Control } from 'react-hook-form';

type Props = {
  control: Control<Record<string, any>>;
  name: string;
  rules: any;
};

const Input: FC<Props> = ({ control, name, rules }) => {
  const { field, meta } = useController({ control, name, rules });

  return (
    <>
      <TextField {...field} />
      <p>{meta.isTouched && 'Touched'}</p>
      <p>{meta.isDirty && 'Dirty'}</p>
      <p>{meta.invalid ? 'invalid' : 'valid'}</p>
    </>
  );
};

export default function App() {
  const { handleSubmit, control } = useForm({
    defaultValues: {
      firstName: '',
    },
  });
  const onSubmit = (data: { firstName: string }) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input control={control} name="firstName" />
      <button type="submit">PUSH</button>
    </form>
  );
}

```

## useFormState
`useForm`に入っているformStateをcontrollerやnameを引数に渡して、便利に使うことができます。

```tsx
import { SubmitHandler, useForm, useFormState } from 'react-hook-form';

type Inputs = {
  firstName: string;
};

export default function Demo() {
  const { register, handleSubmit, control } = useForm<Inputs>({
    defaultValues: {
      firstName: 'firstName',
    },
  });
  const { dirtyFields } = useFormState({
    control,
  });
  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} />
      {dirtyFields.firstName && <p>Field is dirty.</p>}
      <input type="submit" />
    </form>
  );
}
```

## 終わりに
ここまで読んでいただきありがとうございます！
最近registerなどの型も追加されて、Typescriptでさらに書きやすくなりましたね！最近記事書けてなかったのでぼちぼち書いていきたいです。細かいオプションとかは使っていくうちに追記していければと思います。
質問、感想、改善点などをコメントでいただけるとモチベーションにつながりますのでよければお願いします！
