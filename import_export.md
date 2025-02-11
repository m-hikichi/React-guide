# コンポーネントのimportとexport

## コンポーネントを分けよう

Reactでは、1ファイルに1つのコンポーネントを定義することが推奨されています。この方法によって、以下のメリットが得られます。

- 何のためのパーツなのかを明確にするため
  コンポーネントごとに役割を明確にし、それぞれの責任を明確化することで、コードの可読性が向上します。
- 大規模アプリでも管理しやすくするため
  コンポーネントを小さな単位で分けることにより、コードが膨大になった場合でも管理しやすくなります。1つの大きなファイルではなく、機能ごとに小さなモジュールに分けることで、必要な部分をすぐに見つけて修正や追加ができます。
- コンポーネントを分けておくことで再利用できる
  再利用可能なコンポーネントを作成することができるため、コードの重複を減らし、他の部分で使い回すことが可能になります。例えば、同じデザインのボタンやヘッダーを複数のページで使い回せるようにすることで、コードの効率を高めることができます。

## どうやって別ファイルのコンポーネントを使うのか？

Reactでは、コンポーネントを別ファイルに分けて作成した場合、そのコンポーネントを他のファイルで使うには、`export`と`import`を使います。

- **export**: あるファイルからコンポーネントを外部に公開する
- **import**: 他のファイルから公開されたコンポーネントを読み込む

### default export (名前なしexport)

**default export**では、1つのモジュールを名前なしで公開します。これにより、インポート側で任意の名前を使ってそのモジュールを受け取ることができます。

**推奨されるexport方法**  
ReactやJavaScriptでのモジュール設計において、以下の方法が推奨されます。
- **1ファイル=1export**
  1つのファイルには基本的に1つのモジュール（コンポーネントや関数）をexportする形にします。これにより、ファイルが分かりやすくなり、他の開発者がそのファイルを見たときにモジュールの目的が明確になります。
- **1度宣言したアロー関数をdefault export**
  1度宣言したアロー関数をdefault export アロー関数を使用してコンポーネントを作成する場合、そのアロー関数をそのまま`export default`でエクスポートするのが一般的です。アロー関数での定義は簡潔で、短いコンポーネントに向いています。
  ```jsx
  // アロー関数のdefault export
  const Title = (props) => {
    return <h2>{props.title}</h2>
  };
  export default Title;
  ```
- **名前付き関数宣言と同時にdefault export**
  名前付き関数（`function`キーワードを使って定義する関数）も同じく`export default`と一緒に使うことができます。こちらは、関数の名前がはっきりとついているため、デバッグやエラーメッセージが分かりやすくなります。
  ```jsx
  // 名前付き関数のdefault export
  export default function Title(props) {
    return <h2>{props.title}</h2>
  }
  ```

---

### default import (名前なしimport)

- `default export`されたモジュールを、`import モジュール名 from 'ファイルパス'`という形式で読み込みます。
- `default`と明記しなくても、インポートするモジュールに名前をつけて受け取ることができます。

```jsx
// Article.jsx (export元)
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

```jsx
// App.jsx (import先)
import Article from "./components/Article";

function App() {
  return (
    <Article
      title={'新・日本一わかりやすいReact入門'}
      content={'importとexportを使いこなそう'}
    />
  );
}
```

---

### 名前付きexport

名前付きexportでは、1つのファイルから複数のコンポーネントや関数をexportできます。

#### 複数のモジュールをまとめてexport

複数のモジュールをまとめて`export`したいときは、次のようにまとめて`export`できます。

```jsx
// helper.js
export const addTax = (price) => {
    return Math.floor(price * 1.1)
}

export const getWild = () => {
    console.log('Get wild and touch')
}
```

#### エントリポイントでよく使うパターン

Reactアプリケーションでは、エントリポイント（例えば、`index.js`）で複数のモジュールをまとめてエクスポートし、他のファイルからそれらを簡単にインポートできるようにすることがよくあります。この方法によって、ファイル構成が整然として読みやすくなり、必要なモジュールをエントリポイントでまとめて管理することができます。

```jsx
// index.js
export {default as Article} from './Article'
export {default as Content} from './Content'
export {default as Title} from './Title' // 別名exportも併用 (defaultという名前のモジュールをTitleという名前でexport)
```

---

### 名前付きimport

複数のモジュールが1つのファイルでエクスポートされている場合、それらをまとめてimportすることができます。これにより、コードが整理され、どのモジュールを使うのか一目でわかりやすくなります。

```jsx
import {Content, Title} from "./index";

const Article = (props) => {
  return (
    <div>
      <Title title={props.title} />
      <Content content={props.content} />
    </div>
  );
};

export default Article;
```
