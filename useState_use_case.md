# 頻出する`useState`のケース3選

Reactで状態管理を行う際に頻出する`useState`の使い方に関して、3つの主要なケースについて解説します。

## [`useState`の使い方](./useState.md#usestateの使い方)

`useState`はReactで「状態」を管理するために使用するフックです。`useState`は、現在の状態とその状態を更新するための関数（更新関数）を返します。

- `const [state, setState] = useState(initialValue);`
  - `state`: 現在の状態
  - `setState`: 状態を更新するための関数
  - `initialValue`: 初期値（状態の最初の値）

例えば、フォームの入力内容を管理したり、カウントの値を管理したりする場合に使われます。

---

### 1. 引数を使って更新する

これは、よく入力フォームで使用される方法です。フォームの入力値を`useState`で管理し、その値を変更するために`setState`関数を使います。

- `onChange`イベントが発生したときに、そのイベント情報（`event`）から入力内容を取り出して`setName`で更新します。

```jsx
import React, { useState } from 'react';

const TextInput = () => {
  const [name, setName] = useState('');  // nameという状態をuseStateで初期化

  const handleName = (event) => {
    setName(event.target.value);  // 入力内容をnameにセット
  };

  return (
    <input
      onChange={handleName}  // 入力が変わるたびにhandleNameが呼ばれる
      type="text"
      value={name}  // 現在のnameの状態を表示
    />
  );
};
```

#### 解説:
- `onChange`イベントは、フォームに入力されるたびに発生します。イベントオブジェクト（`event`）の`target.value`を取り出して、`setName`で`name`の状態を更新します。
- `value={name}`を設定することで、入力欄に現在の`name`の状態が反映され、ユーザーが入力した内容が表示されます。

---

### 2. `prevState`を活用する

状態の更新時に、現在の状態を基に新しい状態を設定したい場合、`useState`の更新関数では前の状態（`prevState`）を利用することができます。

- 例えば、カウンターのように**状態を前の状態から変更する場合**に便利です。

```jsx
import React, { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0);  // 初期カウント値を0に設定

  const countUp = () => {
    setCount(prevState => prevState + 1);  // 現在のカウント値を1増やす
  };

  const countDown = () => {
    setCount(prevState => prevState - 1);  // 現在のカウント値を1減らす
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

#### 解説:
- `prevState`は、`setCount`に渡されたコールバック関数の引数として渡される、現在の状態（`count`）を表します。
- `setCount(prevState => prevState + 1)`のように、現在の状態に基づいて次の状態を計算することができます。

---

### 3. ON/OFFを切り替えるボタン

`useState`を使って、ON/OFFの状態を切り替えるボタンを作る方法です。このケースでは、`prevState`を使って状態を反転させます。

- 状態の値を反転させるために`!`（論理否定）を使用します。

```jsx
import React, { useState } from 'react';

const ToggleButton = () => {
  const [open, setOpen] = useState(false);  // 初期状態はfalse（OFF）

  const toggle = () => {
    setOpen(prevState => !prevState);  // 現在の状態を反転させる
  };

  return (
    <button onClick={toggle}>
      {open ? 'OPEN' : 'CLOSE'}  {/* openがtrueなら'OPEN'、falseなら'CLOSE'を表示 */}
    </button>
  );
};

export default ToggleButton;
```

#### 解説:
- `setOpen(prevState => !prevState)`では、`prevState`を反転させることで、`true`と`false`が切り替わります。
- ボタンをクリックするたびに、表示されるテキストも`OPEN`と`CLOSE`が切り替わります。

---

## まとめ

- **`useState`の使い方**は、状態を管理するための基本的な方法です。
- **引数を使って更新する**: イベントからデータを取得して状態を更新する。
- **`prevState`を活用する**: 前の状態を基に新しい状態を計算して設定する。
- **ON/OFFを切り替えるボタン**: `prevState`を反転させることで、簡単にトグル（切り替え）操作を実現できます。
