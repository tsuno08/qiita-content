---
title: '[VSCode拡張機能]Styled-Components Extractorが便利すぎる！'
tags:
  - CSS
  - JavaScript
  - React
  - styled-components
private: false
updated_at: '2020-12-13T14:17:28+09:00'
id: 88d70b8c612ea8aeae21
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
styled-componentsの便利な拡張機能を見つけたのでその紹介です。

参考：[Styled-Components Extractor](https://marketplace.visualstudio.com/items?itemName=FallenMax.styled-components-extractor)

## 使い方
拡張機能をインストールしたら、下のような感じで未定義のコンポーネントを書いていきます。未定義なのでエラーが出ています。
※エラーをわかりやすくするためにTypeScriptで書いていますが、JavaScriptでもOKです。

Command+Shift+P(WindowsはCtrl+Shift+P)でコマンドパレットを開いて、
`Extract styled-components` または `Extract exported styled-components`を選択します。


![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/9eff9d11-6178-7224-a406-030fe1c3018b.png)

### Extract styled-components の場合
↓こんな感じでクリップボードにコピーされます。

```tsx
import styled from 'styled-components'
const Page = styled.div``
const Header = styled.div``
const Main = styled.div``
const List = styled.div``
const ListItem = styled.div``
const Footer = styled.div``
```

### Extract exported styled-components の場合
↓こんな感じでexportがついて、クリップボードにコピーされます。

```tsx
import styled from 'styled-components'
export const Page = styled.div``
export const Header = styled.div``
export const Main = styled.div``
export const List = styled.div``
export const ListItem = styled.div``
export const Footer = styled.div``
```

### ショートカット

VSCodeのKeyboard Shortcutsにこんな感じで追加しても使えるみたいです。

>```json
[
  {
    "key": "cmd+alt+e",
    "command": "styledComponentsExtractor.extract",
    "when": "editorFocus"
  },
  {
    "key": "ctrl+alt+e",
    "command": "styledComponentsExtractor.extractExported",
    "when": "editorFocus"
  }
]
```

## 終わりに
ここまで読んでいただきありがとうございます！まだダウンロード数が少ないですが、かなり便利だと思います。
他に便利な拡張機能などがあれば教えていただけると嬉しいです。



