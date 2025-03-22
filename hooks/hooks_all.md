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
