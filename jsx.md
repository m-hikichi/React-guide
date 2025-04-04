# JSXの記法

JSX（JavaScript XML）は、JavaScript内でHTMLライクな構文を使用するための拡張言語です。これを使うことで、Reactコンポーネントの構造を簡潔に記述できるようになります。

## 目次
1. [JSXとは](#jsxとは)
2. [なぜJSXを使うのか](#なぜjsxを使うのか)
3. [JSXが行っている処理](#jsxが行っている処理)
4. [JSXの基礎文法](#jsxの基礎文法)
5. [JSXの特殊な構文](#jsxの特殊な構文)

## JSXとは

JSXは、JavaScriptとHTMLを組み合わせて記述できる構文です。HTMLのようなタグをJavaScript内で使えるようになり、ReactでUIコンポーネントを作成する際に非常に直感的でわかりやすくなります。

Reactでは、最終的にJSXは`React.createElement`に変換され、実際のUI要素が生成されます。以下はJSXを使った簡単な例です：

```jsx
const BlueButton = () => {
  return (
    <button className={'btn-blue'}>
      Click me!
    </button>
  )
}
```

## なぜJSXを使うのか

JSXを使わない場合、UI要素を生成するために`React.createElement`を直接使う必要があり、非常に冗長になります。例えば、以下のようにコードになります：

```jsx
React.createElement(
  'button',
  {className: 'btn-blue'},
  'Click me!'
)
```

これが簡潔で直感的な構文に変わるのがJSXの大きな利点です：

```jsx
<button className={'btn-blue'}>
  Click me!
</button>
```

JSXを使うことで、HTMLのような記述ができ、UIのコードが格段に読みやすくなります。

## JSXが行っている処理

JSXは、コンパイル時に以下の処理を行います：

1. **JSX → `React.createElement`に変換**  
   JSXは最終的に`React.createElement`を呼び出すコードに変換されます。これによって、React要素が生成され、Reactがその要素を管理します。

2. **React要素を生成**  
   生成されたReact要素は、ブラウザのDOMと同期し、ユーザーインターフェースを更新します。

JSXを使うことで、`React.createElement`を直接書くことなく、簡潔にコンポーネントを作成できるため、コードの可読性と保守性が向上します。

## JSXの基礎文法

JSXを使うためにはいくつかの基本的なルールがあります。特にReactのコンポーネントを記述する際には、これらのルールを守る必要があります。

### 1. Reactをインポートする
JSXを使うファイルでは、必ず`React`をインポートする必要があります。  
※ React v17以降からこの記述は不要

```jsx
import React from 'react';
```

### 2. `return`文内でJSXを記述
JSXは通常、`return`文の中で記述されます。基本的にHTMLと同じような構文ですが、`class`属性は`className`に変更されます。

```jsx
const BlueButton = () => {
  return (
    <button className={'btn-blue'}>
      Click me!
    </button>
  )
}
```

### 3. キャメルケース
JSX内で使用する属性は、HTMLの属性とは異なり、キャメルケース（camelCase）で記述します。

### 4. `{}`内で変数を扱う
JSX内でJavaScriptの変数を使いたい場合は、`{}`で囲む必要があります。これによって、JavaScriptコードとHTML要素がシームレスに統合されます。

```jsx
const Thumbnail = () => {
  const caption = 'トラハックのかっこいい画像';
  const imagePath = '/img/torahack.png';

  return (
    <div>
      <p>{caption}</p>
      <img src={imagePath} alt={'トラハック'}/>
    </div>
  )
}
```

### 5. 閉じタグが必要
JSXでは、すべてのタグは閉じる必要があります。`<img />`のように、自己完結型のタグでも必ず閉じタグを使用します。


## JSXの特殊な構文

### JSXは必ず1つの親要素で囲む必要がある
JSXでは、`return`文内に複数の要素を並列に記述することはできません。必ず1つの親要素で囲む必要があります。これがないとエラーになります。

- **エラー例**  
  ```jsx
  return (
    <p>新・日本一わかりやすいReact入門</p>
    <p>JSXの基礎文法を解説します。</p>
  )
  ```

- **修正例**  
  ```jsx
  return (
    <div>
      <p>新・日本一わかりやすいReact入門</p>
      <p>JSXの基礎文法を解説します。</p>
    </div>
  )
  ```

### `React.Fragment`を使う
上記では`<div>`タグで囲んでいるのですが、実際に`<div>`タグを使うと、HTMLの中に不要な`div`タグが増えてしまうことがあります。これが、特に多くの要素を囲う場合には不必要なHTML構造を作り出すことになり、DOMが冗長になってしまう可能性があります。  
この問題を解決するために、`React.Fragment`を使う方法があります。`React.Fragment`は、複数の要素を親要素で囲う際に、実際のDOMに`div`タグのような不必要な要素を追加せず、単に仮のラッパーとして機能します。

```jsx
return (
  <React.Fragment>
    <p>新・日本一わかりやすいReact入門</p>
    <p>JSXの基礎文法を解説します。</p>
  </React.Fragment>
)
```

- **省略形**  
  `React.Fragment`は省略形（`<>`と`</>`）で記述できます。

  ```jsx
  return (
    <>
      <p>新・日本一わかりやすいReact入門</p>
      <p>JSXの基礎文法を解説します。</p>
    </>
  )
  ```

このように、JSXを使うことでReactのコードがより簡潔に、視覚的にわかりやすくなります。理解しやすい構文に従い、効率的なReactコンポーネントを作成しましょう。