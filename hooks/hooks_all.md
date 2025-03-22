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
