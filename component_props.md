# コンポーネントとprops

Reactでは、コンポーネントを使ってユーザーインターフェース（UI）を構築します。コンポーネントは、UIの見た目や機能を持つ再利用可能な部品として設計され、複雑なアプリケーションも小さな部品に分けて作ることができます。

## 目次
1. [コンポーネントとは](#コンポーネントとは)
2. [コンポーネントの種類](#コンポーネントの種類)
3. [なぜコンポーネントを使うのか？](#なぜコンポーネントを使うのか)
4. [コンポーネントの基本的な使い方](#コンポーネントの基本的な使い方)
   - [propsでデータを受け渡す](#propsでデータを受け渡す)
   - [propsで受け渡せるデータの種類](#propsで受け渡せるデータの種類)
   - [コンポーネントの再利用](#コンポーネントの再利用)


## コンポーネントとは

コンポーネントは、UIの見た目や動作、状態（state）を管理するための独立した部品です。コンポーネントを組み合わせて、WEBページやアプリケーションの画面を構築します。コンポーネントは再利用可能で、開発者は同じUI部品を何度も書くことなく、効率よくアプリケーションを作成できます。

## コンポーネントの種類

Reactには主に2種類のコンポーネントがあります。

- **クラスコンポーネント (Class Component)**

  以前は、クラスコンポーネントを使って状態管理やライフサイクルメソッドを実装していましたが、現在は関数コンポーネントが主流です。クラスコンポーネントの例を以下に示します。

  ```jsx
  import React, { Component } from 'react';
  
  class Button extends Component {
    render() {
      return <button>Say, {this.props.hello}</button>;
    }
  }
  
  export default Button;
  ```

- **関数コンポーネント (Functional Component)**

  関数コンポーネントは、よりシンプルで軽量なコンポーネントです。状態やライフサイクルの管理は、React Hooksを使うことで関数コンポーネントでも可能になりました。現在では、関数コンポーネントが主流で使われています。

  ```jsx
  import React from 'react';
  
  const Button = (props) => {
    return <button>Say, {props.hello}</button>;
  };

  export default Button;
  ```

## なぜコンポーネントを使うのか？

Reactでコンポーネントを使う理由は以下の通りです：

- **再利用性**  
  コンポーネントを使うと、同じUI部品を何度も使い回すことができ、コードの重複を避けられます。
- **コードの見通しが良くなる**  
  コンポーネントごとにファイルを分けることで、コードが整理されて読みやすくなります。例えば、1コンポーネント = 1ファイルという形で、ファイルが分割されます。
- **変更に強くなる**  
  コンポーネントは独立しているので、修正が1か所で済みます。変更が他の部分に影響を与えることを防げます。


## コンポーネントの基本的な使い方

Reactでは、親コンポーネントが子コンポーネントを呼び出して、UIを組み立てます。

**親コンポーネント（App.jsx）**

```jsx
import Article from './components/Article';

function App() {
  return (
    <div>
      <Article />
    </div>
  );
}

export default App;
```

**子コンポーネント（components/Article.jsx）**

```jsx
const Article = () => {
  return <h2>こんにちは</h2>;
};

export default Article;
```

**コンポーネントの命名規則**  
- **ファイル名は大文字**で始める（例：`Article.jsx`）。
- 子コンポーネントはファイル内で `export` し、親コンポーネントで `import` して使います。
- 関数宣言にはいくつか方法があります。上記の例ではアロー関数を使用していますが、通常の関数宣言も可能です。

---

### propsでデータを受け渡す

親コンポーネントから子コンポーネントにデータを渡すには、**props**（プロパティ）を使用します。

**親コンポーネント (App.jsx)**
```jsx
import Article from './components/Article';

function App() {
  return (
    <div>
      <Article
        title="新・日本一わかりやすいReact入門"
        content="今日のトピックはpropsについて"
      />
    </div>
  );
}

export default App;
```

**子コンポーネント (Article.jsx)**
```jsx
const Article = (props) => {
  return (
    <div>
      <h2>{props.title}</h2>
      <p>{props.content}</p>
    </div>
  );
};

export default Article;
```

**重要ポイント**  
- **propsは引数として受け取る**：子コンポーネントは、関数の引数として `props` を受け取り、その中に渡されたデータを使います。
- **データを動的に渡す**：親から子に動的なデータを渡し、子コンポーネントで利用することができます。

---

### propsで受け渡せるデータの種類

propsにはさまざまなデータを渡すことができます。以下のデータ型が可能です：
- 文字列 (String)
- 数値 (Number)
- 真偽値 (Boolean)
- 配列 (Array)
- オブジェクト (Object)
- 日付 (Date)
- その他の変数や式

```jsx
import Article from './components/Article';

function App() {
  const authorName = 'Torahack';
  const now = new Date();
  
  return (
    <div>
      <Article
        title="新・日本一わかりやすいReact入門"
        content="今日のトピックはpropsについて"
        order={3}
        isPublished={true}
        authorName={authorName}
        updatedAt={now}
      />
    </div>
  );
}

export default App;
```

---

### コンポーネントの再利用

同じコンポーネントを何度も使うことができます。データを変えれば、同じコンポーネントを異なる内容で表示することができます。
この手法により、コンポーネントを再利用することでコードの重複を減らし、効率的にUIを構築することができます。例えば、複数のコンポーネントに同じ構造を持たせつつ、それぞれ異なるデータを渡すことで、内容を動的に変更できます。

一般的には、配列データをmap()メソッドで処理して、同じコンポーネントを繰り返し描画する方法がよく使われます。これにより、リストや複数のアイテムを簡単に表示できます。

```jsx
import Article from './components/Article';

function App() {
  return (
    <div>
      <Article
        title="新・日本一わかりやすいReact入門4"
        content="今日のトピックはpropsについて"
      />
      <Article
        title="新・日本一わかりやすいReact入門5"
        content="今日のトピックはuseStateについて"
      />
      <Article
        title="新・日本一わかりやすいReact入門6"
        content="今日のトピックはuseEffectについて"
      />
    </div>
  );
}

export default App;
```
