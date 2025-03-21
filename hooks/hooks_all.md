## useEffect

### 概要

`useEffect`は、副作用（サイドエフェクト）を処理するためのフックです。副作用とは、UIのレンダリング以外でコンポーネント内で実行する必要がある処理を指します。例えば、データのフェッチ、イベントリスナーの追加・削除、タイマーの設定などです。

`useEffect`は 副作用を「いつ」実行するかをコントロール することができます。具体的には、`useEffect`は 依存配列 を設定することで、副作用を実行するタイミングを指定することができます。

#### 副作用とは

React コンポーネントは通常、UIのレンダリングを管理しますが、UIの描画以外で何かを実行したい場合、それが 副作用（Side Effect） です。例えば：

- データの取得（APIリクエスト）
- タイマーやイベントリスナーの設定
- 外部ライブラリの操作やDOMの直接操作

### 使い方

```jsx
import React, { useEffect, useState } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/data')
      .then((response) => response.json())
      .then((data) => setData(data));
  }, []);  // 空の配列が依存配列 => コンポーネントが初めてレンダリングされるときのみ実行

  return <div>{data ? JSON.stringify(data) : 'Loading...'}</div>;
}
```

#### 第二引数を使って発火タイミングを指定

`useEffect`の第二引数は、どのタイミングで副作用を実行するかを指定します。これにより、再レンダリング時に副作用を実行するか、またはしないかを制御できます。

1. **初回レンダリング時のみ実行**

依存配列に空の配列`[]`を渡すと、コンポーネントが初めてレンダリングされた後のみ実行 されます。

```jsx
useEffect(() => {
  // 初回レンダリング後に実行される副作用
  console.log('コンポーネントがマウントされました');
}, []);  /
```

2. **指定した状態やプロパティが変更されたときに実行**

依存配列に指定した状態やプロパティを渡すと、その値が変わったタイミングで副作用が実行されます。

```jsx
useEffect(() => {
  // count が変わる度に実行される副作用
  console.log(`カウントが更新されました: ${count}`);
}, [count]);  // count が変わるたびに実行
```

3. **毎回のレンダリング後に実行**

依存配列を渡さない場合、毎回レンダリング後 に副作用が実行されます。

```jsx
useEffect(() => {
  // 毎回レンダリング後に実行される副作用
  console.log('毎回レンダリング後に実行される');
});  // 依存配列なし => 毎回レンダリング後に実行
```

#### クリーンアップ

`useEffect`では、副作用が不要になった場合にクリーンアップ処理を実行することもできます。例えば、タイマーやイベントリスナーを解除する際に使います。

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log('毎秒処理');
  }, 1000);

  // クリーンアップ処理
  return () => clearInterval(timer);
}, []);  // コンポーネントがアンマウントされる時にタイマーをクリア
```

---

## useContext

### 概要

`useContext`は、React で「コンテクスト」と呼ばれるグローバルな状態を、コンポーネントツリー内で共有するためのフックです。これにより、`props`を何度も中間のコンポーネントに渡す手間を省くことができます。例えば、アプリケーション全体で共通のテーマ設定やユーザー情報を扱いたい場合などに便利です。

### 使い方

```jsx
import React, { useContext } from 'react';

const ThemeContext = React.createContext();

function ThemedComponent() {
  const theme = useContext(ThemeContext);

  return <div style={{ background: theme.background, color: theme.color }}>Hello, World!</div>;
}

function App() {
  const theme = { background: 'black', color: 'white' };

  return (
    <ThemeContext.Provider value={theme}>
      <ThemedComponent />
    </ThemeContext.Provider>
  );
}
```

#### `createContext`を使用してグローバルなコンテクストを宣言

`useContext`を使うためには、まず`createContext`を使ってコンテクストを作成する必要があります。
`createContext`によって生成されたコンテクストは、Reactコンポーネントツリー全体でデータを共有できるようになります。

```jsx
const ThemeContext = React.createContext();
```

#### グローバルでコンテクストを提供するために`Provider`で囲む

コンテクストでデータを共有するためには、`Provider`コンポーネントを使用して、コンテクストを提供する必要があります。`Provider`は`value`プロパティを通じてコンポーネントツリーにデータを供給します。
```jsx
function App() {
  const theme = { background: 'black', color: 'white' };

  return (
    // Provider でコンテクストを囲む
    <ThemeContext.Provider value={theme}>
      <ThemedComponent />
    </ThemeContext.Provider>
  );
}
```

#### `useContext`を使ってコンテクストを取得

コンテクストが提供された後、コンポーネント内でその値を取得するには、`useContext`フックを使います。`useContext`には、コンテクストオブジェクトを渡すことで、その現在の値にアクセスできます。

```jsx
import React, { useContext } from 'react';

function ThemedComponent() {
  const theme = useContext(ThemeContext);

  return <div style={{ background: theme.background, color: theme.color }}>Hello, World!</div>;
}
```

---

## useRef

### 概要

`useRef`は、参照を作成するためのフックで、DOM要素や任意の変数を保持できます。レンダリング間で値を保持できるため、再レンダリングをトリガーしないデータの管理に適しています。

### 使い方

```jsx
import React, { useRef } from 'react';

function FocusInput() {
  const inputRef = useRef(null);

  const logInputValue = () => {
    // inputRef.current にアクセスして、入力された文字列を取得
    console.log(inputRef.current.value);
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus the input</button>
    </div>
  );
}
```

#### **参照用のフックを作成**

`useRef`を使って、DOM要素や任意の値にアクセスするための参照を作成します。
```jsx
const inputRef = useRef(null);
```

#### `ref`プロパティに指定したタグの情報にアクセス

`useRef`で作成した参照 (`inputRef`など) を、HTML タグの`ref`プロパティに設定することで、特定の DOM 要素にアクセスできます。これにより、`inputRef.current`を使ってその要素のプロパティやメソッドにアクセスできるようになります。
```jsx
<input ref={inputRef} type="text" />
```

---

## useReducer

---

## useMemo

---

## useCallback

---

## カスタムHooks
