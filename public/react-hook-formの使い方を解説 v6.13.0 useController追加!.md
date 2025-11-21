---
title: react-hook-formの使い方を解説 v6.13.0 useController追加!
tags:
  - JavaScript
  - TypeScript
  - React
  - react-hook-form
private: false
updated_at: '2021-09-29T09:43:42+09:00'
id: 0140acaee87b7c4ed856
organization_url_name: null
slide: false
ignorePublish: false
---
久しぶりの投稿になります。今後は週一記事以上は書けるように頑張ります！今回はreact-hook-formについての記事です。公式ドキュメントの内容を、自分なりにまとめてみました。より詳しく知りたい方は公式ドキュメントも見てみて下さい！
追記（2020/11/28）：react-hook-formのv6.12.0で便利そうな機能がリリースされたので、それもregisterの部分に追加しました。

追記  (2021/09/29) version7以降の記事を別で書きました！
[react-hook-formの使い方まとめ](https://qiita.com/NozomuTsuruta/items/60d15d97eeef71993f06)

参考：[公式ドキュメント](https://react-hook-form.com/)

## react-hook-formとは
inputとかのformに関係するデータを使う時に、Stateを使わなくて良くて、レンダリング回数を減らせる！

## インストール

```zsh
## yarnの場合
yarn add react-hook-form

## npmの場合
npm install react-hook-form
```

以上！TypeScriptの型定義も含まれてます。

↓↓↓簡単な例

```react
import React from 'react';
import { useForm } from 'react-hook-form';
import { ErrorMessage } from '@hookform/error-message';

type FormInputs = {
  name: string;
  email: string;
};

export const Demo = () => {
  const { register, errors, handleSubmit, reset } = useForm<FormInputs>();

  const on_submit = (data: FormInputs) => {
    console.log(data);
    reset();
  };

  return (
    <form onSubmit={handleSubmit(on_submit)}>
      <input name="name" ref={register} />
      <input
        name="email"
        ref={register({
          required: true,
          maxLength: 60,
          pattern: {
            value: /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/,
            message: 'メールアドレスの形式が不正です',
          },
        })}
      />
      <button type="submit">Submit</button>
      <ErrorMessage errors={errors} name="email" message={errors.email?.message} />
    </form>
  );
};
```

↓この例だと入力した値はこんな風に出力される。それぞれの機能を解説して行きます。

```json
{name: 入力した値,email: 入力した値}
```

## useForm
react-hook-formからインポートするものはいくつかあるが、ほとんどuseFormから取得することが多い。


```react
const { register, reset, handleSubmit } = useForm({
  mode: onSubmit,
  defaultValues: {name: "aaa", email: test@test.com}
})
```

引数にオブジェクトで色んな設定みたいなのができる

||||
|---|---|---|
|mode|初回のバリデーションの実行タイミング<br>(初期値：onSubmit)|onChange or onBlur or onSubmit or onTouched or all|
|reValidateMode|初回のバリデーション実行後、次の実行タイミング<br>(初期値： onChange)|onChange or onBlur or onSubmit|
|defaultValues|初期値をオブジェクトで入力します<br>(resetでdefaultValuesの値に戻る)|{name属性の値: 初期値,...}|
|resolver|他のvalidationライブラリを使う時||
|shouldUnregister|入力要素がアンマウントされたら、値の参照を解除、falseなら入力された値を保持<br>(初期値： true)|true or false|

### register

inputなどに入力された値を参照するために使う。name属性を設定する必要があり、registerはrefに入れる。UIライブラリを使う場合は`control`参照。一番使う。

↓nameの付け方によって、dataの形が変わる.


>name="firstName"	→{ firstName: 'value'}
name="firstName[0]"	→{ firstName: [ 'value' ] }
name="name.firstName"	→{ name: { firstName: 'value' } }
name="name.firstName[0]"	→{ name: { firstName: [ 'value' ] } }

引数にオブジェクトで色んなバリデーションが設定できる.

||||
|---|---|---|
|required|必須項目にするか|true or false or エラーメッセージ|
|maxLength|最大文字数|{value: number, message: エラーメッセージ}|
|minLenght|最小文字数|{value: number, message: エラーメッセージ}|
|max|最大データ量|{value: number, message: エラーメッセージ}|
|min|最小データ量|{value: number, message: エラーメッセージ}|
|pattern|文字の形式|{value: 正規表現, message: エラーメッセージ}|
|validate|カスタム|{任意の名前: value => booleanを返す式 or 関数 &#124;&#124; エラーメッセージ} |
|valueAsNumber <font color="red">new!</font>|数値を返す、問題があればNaNを返す|true or false|
|valueAsDate <font color="red">new!</font>|日付を返す、問題があればnullを返す|true or false|
|setValueAs <font color="red">new!</font>|関数に値を通す|例）setValueAs:(value) => trim(value)|

```react
<input
   name="email"
   ref={register({
   required: true,
   maxLength: 60,
   pattern: {
       value: /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/,
       message: 'メールアドレスの形式が不正です',
      },
   })}
   validate: {
       lessThanTen: value => parseInt(value, 60) < 60 || '60文字以内で入力して下さい'
   }
/>
```

### unregister
引数に入れたnameはregisterで参照しなくなる。配列を入れて、複数も可能。`unregister(['name','email'])`

```react
<button onClick={() => unregister("email")}>email_unregister</button>
```

### errors

registerなどで設定したバリデーションを通らなかった時にエラーのデータが入る。エラーメッセージを設定している場合は、それも取得できる。

```react
<input name="name" ref={register({required: '入力して下さい'}) />
{errors.name && <p>errors.name.message</p>}
```

### watch
第一引数入れたnameの値を監視。第二引数は初期値を設定可能。unregisterと同様に配列に入れて複数も可能。何も入れないと、全てのnameの値を取得。レンダリング回数は増える。

```react
const watch_name = watch("name", false);
const watch_all_fields = watch();
const watch_fields = watch(["name", "email"]);
```

### handleSubmit
ラップした関数にformのdataをオブジェクトの形で渡してくれる。`on_submit`の引数にdataがあるのはそのため。
`event.preventDefalut()`も必要なし。

```react
const on_submit = (data: FormInputs) => {
    console.log(data);
};

return (
    <form onSubmit={handleSubmit(on_submit)}>
...
```

###reset

formの中の状態を初期化する関数。`defaultValue`が設定されていたら、その値になる

```react
const on_click =()=>{
  reset();
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
const single_value = getValues("name");
const multiple_values = getValues(["name", "email"]);
```

### setValue
第一引数に入れたnameに第二引数に入れた値をセットする関数。その際に`shouldValidate`でバリデーションを実行するかどうか、`shouldDirty`で変更後に変更の有無の判定を行うかどうか

```react
<button onClick={()=> setValue('name', 'value', { shouldValidate: true, shouldDirty: true })}>
```

### trigger
引数に入れたnameのバリデーションを実行する関数。こちらも配列で複数、空なら全体。

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
form内の入力の有無や送信の状態などを取得できる。`formState.isSubmitting`はbuttonにローディングをつける時によく使う。

||||
|---|---|---|
|isDirty|全体で何かしら変更があったらtrueになる|true or false|
|dirtyFields|それぞれの入力要素で何かしらの変更があったらtrue|{name属性の値: true or false,...}|
|touched|それぞれの入力要素で何かしらの操作をしたらtrueになる<br>（フォーカスするだけでも、onBlurのタイミングで反映される）|{name属性の値: true or false,...}|
| isSubmitted |formが送信されたらtrue、resetでfalseに戻る|true or false|
| isSubmitSuccessful |formの送信が成功したらtrue|true or false|
|isSubmitting|formの送信中にtrue|true or false|
| submitCount |formの送信された回数|number|
|isValid|何かしらエラーがあったらtrue<br>(modeがonChange or onBlurの時のみ)|true or false|
|errors|バリデーションのエラーのオブジェクトが入る|{name属性の値: {<br>type: "required",<br>message: "",<br>ref: \<input name="name属性の値" type="text"></input>,<br>},...}|

```react
import React from "react";
import { useForm } from "react-hook-form";

export const Demo = () => {
  const { register, handleSubmit, errors, formState } = useForm();
  const on_submit = data => console.log(data);

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
  render={({ onChange, onBlur, value, name, ref }) => (
    <TextField
      onBlur={onBlur}
      onChange={onChange}
      checked={value}
      inputRef={ref}
    />
  )}
/>

```

## ErrorMessage
指定した`name`のエラーメッセージを表示できる。`message`はstringならOK

```react
<ErrorMessage errors={errors} name="email" message={errors.email?.message} />
```

## useFormContext,FormProvider
`FormProvider`に`useForm`の`register`や`errors`などをまとめて渡して、ラップされたコンポーネントで`useFormContext`を使うと、その渡された値を使うことができる。

```react
import React from "react";
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
  return <input name="name" ref={register} />;
}

```
## useWatch
controlを渡すことで`watch`を別のコンポーネントでも使えるようにした感じ。

```react
import React from "react";
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
|remove|特定のinputを削除、引数無しで全削除|remove(1) remove()|


```react
import React from 'react';
import { useForm, useFieldArray } from 'react-hook-form';

export const Demo = () => {
  const { register, control, handleSubmit } = useForm({
    defaultValues: {
      demo: [{ name: 'name', email: 'email@email.com' }],
    },
  });
  const { fields, append, prepend, remove } = useFieldArray({
    control,
    name: 'demo',
  });

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      {fields.map((item, index) => (
        <div key={item.id}>
          <input
            name={`demo[${index}].name`}
            ref={register}
            defaultValue={item.name}
          />
          <input
            name={`demo[${index}].email`}
            ref={register}
            defaultValue={item.email}
          />
          <button type="button" onClick={() => remove(index)}>
            Delete
          </button>
        </div>
      ))}
      <button
        type="button"
        onClick={() =>
          append({ name: 'appendName', email: 'append@email.com' })
        }
      >
        append
      </button>
      <button
        type="button"
        onClick={() =>
          prepend({
            name: 'prependName',
            email: 'prepend@email.com',
          })
        }
      >
        prepend
      </button>
      <input type="submit" />
    </form>
  );
};


```

## useController <font color="red">new!</font>
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
      <Input control={control} name="firstName" rules={{ required: true }} />
      <button type="submit">PUSH</button>
    </form>
  );
}

```

## 終わりに
ここまで読んでいただきありがとうございます！
少しでもreact-hook-formの理解が深まれば幸いです。自分も書きながら、react-hook-formの便利さが機能が理解できました。
質問、感想、改善点などをコメントでいただけるとモチベーションにつながりますのでよければお願いします！
