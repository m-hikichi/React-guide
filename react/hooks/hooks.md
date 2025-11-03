# コンポーネントの状態管理

## Hooksとは?

Reactで「状態管理」をするための手段です。状態とは、コンポーネント内で扱うデータ（例えばボタンの押下回数や入力フォームの内容など）を指します。これを効率よく管理するための仕組みが「Hooks」です。

### クラスコンポーネントと関数コンポーネント

**Reactの古いやり方**では、コンポーネントを作るには「クラスコンポーネント」を使う必要がありました。クラスコンポーネントでは、次のようなことができます。

- コンポーネント内で状態を管理するための `state`
- コンポーネントの「ライフサイクル」に基づいたメソッド

ですが、Reactの進化により、**関数コンポーネントでも状態管理が可能になりました**。これを実現するために登場したのが「Hooks」です。

### Hooksの登場

Hooksは、従来クラスコンポーネントでしか使えなかった `state` やライフサイクルの処理を、**関数コンポーネントでも使えるようにする機能です**。

関数コンポーネントはシンプルで書きやすいため、Reactの開発者たちはこれを好むようになりました。Hooksにより、関数コンポーネントでも状態管理が可能になり、結果的に**関数コンポーネントが主流**になりました。

つまり、**Hooks = クラスコンポーネントでできたことを関数コンポーネントでできるようにする仕組み**です。


## なぜ`state`を使うのか?

Reactでコンポーネントの状態（`state`）を管理する理由は、主に次の2つです。

### コンポーネントの内部データを動的に変更したい

Reactでは、**コンポーネントの表示内容を状態（`state`）に基づいて動的に更新する**ことが重要です。例えば、ボタンがクリックされた回数を表示する場合、その回数はコンポーネント内で状態として管理され、ユーザーがボタンをクリックするたびに表示が更新されます。

ここで注意したいのは、**状態を変更するとコンポーネントが再描画される**という点です。これがReactの強みの1つであり、ユーザーインターフェースが状態に基づいて自動的に更新されます。

**✖ NGな方法**：
コンポーネント内の要素をJavaScriptのように取得して直接操作（例：DOMを直接変更）する方法です。これでは、Reactが持つ「仮想DOM」のメリットが失われてしまいます。

**〇 OKな方法**：
`state`を使って、状態が変更されたことをReactに伝えます。これにより、Reactは自動的にコンポーネントを再描画（再レンダリング）し、最新の状態を画面に反映させます。

### Reactコンポーネントの再描画（再レンダリング）をトリガーする2つの要因
  
Reactのコンポーネントが再描画されるきっかけは、主に次の2つです。

- **`state`（状態）が変更されたとき**
  `state`が変更されると、Reactは自動的にそのコンポーネントを再描画します。これによって、最新の状態が画面に表示されます。

- **`props`が変更されたとき**
  コンポーネントは親コンポーネントからデータ（`props`）を受け取って表示します。その`props`が変わると、コンポーネントも再描画されます。

この2つの要因があることで、Reactは常に最新の情報をユーザーに表示し続けることができます。

## `props`と`state`の違い

`props`と`state`は両方ともコンポーネントが再描画されるきっかけになりますが、それぞれの役割は異なります。

- `props`: 親コンポーネントから子コンポーネントに渡される値です。子コンポーネントは`props`を変更できません（読み取り専用）。`props`は関数の引数のようなものと考えてください。

- `state`: コンポーネント内で宣言し、内部で制御する値です。コンポーネント内で変更可能で、変更することでそのコンポーネントが再描画されます。

## `state`を`props`に渡す

`state`の値を親コンポーネントから子コンポーネントに渡すことがよくあります。`state`を渡す方法と注意点を見ていきましょう。

- **更新関数はそのまま渡さず、関数化する**
  `state`を直接渡すのは問題ありませんが、`setState`（更新関数）をそのまま渡すのは避け、**関数を経由して渡す**方が良いです。

  **OKな例**
  ```jsx
  <PublishButton isPublished={isPublished} onClick={publishArticle} />
  ```
  ```jsx
  <PublishButton isPublished={isPublished} onClick={() => publishArticle()} />
  ```

  **NGな関数の渡し方（無限レンダリングが起きる）**
  この方法では、`publishArticle`が即座に実行されるため、無限に再描画が発生してしまうことがあります。**関数を実行するのではなく、関数自体を渡す**必要があります。
  ```jsx
  <PublishButton isPublished={isPublished} onClick={publishArticle()} />
  ```

### 例

```jsx
const Article = (props) => {
  const [isPublished, setIsPublished] = useState(false)
  const publishArticle = () => {
    setIsPublished(true)
  }
  return {
    <div>
      <Title title={props.title} />
      <Content content={props.content} />
      <PublishButton isPublished={isPublished} onClick={publishArticle} />
    </div>
  };
};
```

```jsx
const PublishButton = (props) => {
  return (
    <button onClick={() => props.onClick()}>
      公開状態: {props.isPublished.toString()}
    </button>
  )
}
export default PublishButton;
```

**ポイント**:
- `props.onClick()`は親コンポーネントで定義した`publishArticle`関数に対応します。
- `PublishButton`では、ボタンがクリックされると`props.onClick()`（`publishArticle`）が実行され、状態が更新されます。
