# useState

Reactの`useState`フックは、コンポーネント内で状態（`state`）を管理するための基本的かつ強力な仕組みです。ここでは、`useState`の概要、基本的な使い方、そしてよくある3つの利用ケースについて解説します。

---

## 1. useStateの概要

- **目的**  
  コンポーネント内で動的なデータ（例：フォームの入力値、カウント値、ON/OFF状態など）を管理し、その値が変更されたときに自動的に再レンダリングを実行するために使います。

- **基本構文**  
  ```jsx
  const [state, setState] = useState(initialState);
  ```
  - `state`: 現在の状態（値）を保持する変数
  - `setState`: 状態を更新するための関数
  - `initialState`: コンポーネントの初回レンダリング時に設定される初期値

- **重要ポイント**  
  状態を更新する場合、直接変数に値を代入しても再レンダリングは行われません。必ず`setState`を使用して状態を更新する必要があります。

---

## 2. 基本的な使い方

### 状態の宣言と更新

1. **状態の宣言**  
   ```jsx
   const [count, setCount] = useState(0);
   ```
   - `count`には現在の値が保持され、`setCount`を通じて新しい値に更新します。

2. **状態の更新**  
   ```jsx
   setCount(count + 1);
   ```
   - このように更新関数を呼び出すことで、Reactはコンポーネントを再レンダリングし、新しい状態が反映されます。

### 具体例：カウンターコンポーネント

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

---

## 3. よくあるuseStateの利用ケース

### ケース1: イベント引数を使って状態を更新する

- **用途**  
  入力フォームなど、ユーザーの操作に応じた値の更新

- **実装例**  
  ```jsx
  import React, { useState } from 'react';

  const TextInput = () => {
    const [name, setName] = useState('');

    const handleName = (event) => {
      setName(event.target.value);
    };

    return (
      <input
        onChange={handleName}
        type="text"
        value={name}
      />
    );
  };
  ```
  
- **ポイント**  
  - `onChange`イベントで発生する`event.target.value`を使って状態を更新します。  
  - `value`属性に状態をバインドすることで、常に最新の入力値が表示されます。

---

### ケース2: prevState（前の状態）を活用する更新

- **用途**  
  前の状態に基づいた更新（例：カウントの増減など）

- **実装例**  
  ```jsx
  import React, { useState } from 'react';

  const Counter = () => {
    const [count, setCount] = useState(0);

    const countUp = () => {
      setCount(prevState => prevState + 1);
    };

    const countDown = () => {
      setCount(prevState => prevState - 1);
    };

    return (
      <div>
        <p>現在のカウント数: {count}</p>
        <button onClick={countUp}>up</button>
        <button onClick={countDown}>down</button>
      </div>
    );
  };

  export default Counter;
  ```

- **ポイント**  
  - `setCount`に渡すコールバック関数の引数`prevState`を使って、現在の状態から新たな状態を計算します。  
  - 複数回更新が重なる場合にも、正確な状態更新が可能になります。

---

### ケース3: ON/OFFを切り替えるトグルボタン

- **用途**  
  Boolean型の状態（例：表示/非表示、ON/OFF）を簡単に切り替える場合

- **実装例**  
  ```jsx
  import React, { useState } from 'react';

  const ToggleButton = () => {
    const [open, setOpen] = useState(false);

    const toggle = () => {
      setOpen(prevState => !prevState);
    };

    return (
      <button onClick={toggle}>
        {open ? 'OPEN' : 'CLOSE'}
      </button>
    );
  };

  export default ToggleButton;
  ```

- **ポイント**  
  - Boolean値を反転させるために、`!`（論理否定）を使っています。  
  - ボタンのクリックで状態が切り替わり、表示テキストも変化します。

---

## まとめ

- **基本概念**: useStateは、Reactコンポーネント内で状態管理をシンプルかつ効率的に行うためのフックです。
- **状態の宣言と更新**: `const [state, setState] = useState(initialState);` の形で宣言し、必ず更新関数（setState）を使用して状態変更を行います。
- **具体例と活用法**:
  - **イベント引数を使う場合**: ユーザーの入力値を取得して状態更新を行う。
  - **prevStateを利用する場合**: 前の状態に基づいた計算で状態を変更する（例：カウンター）。
  - **トグル操作**: Boolean値を反転させ、ON/OFFを簡単に切り替える操作を実現する。
