# React Hooks 入門：useState

`useState`フックは、React コンポーネントの中で「変化する値（状態 / state）」を扱うための、一番基本的なフックです。
この資料では、`useState`の概要・基本的な使い方・よくある利用パターン・つまずきポイントを、初心者向けに解説します。

---

## 1. そもそも「状態（state）」とは？

React では、画面に表示する情報を **「状態（state）」** として管理します。

* 例

  * カウンターの現在の数値
  * 入力フォームの文字
  * ON/OFF や 表示/非表示 のフラグ
* 特徴

  * 画面上で「変化する」値
  * その値が変わると、React が自動的にコンポーネントを再レンダリングしてくれる

逆に、「固定の設定値」や「親から渡される変更不可の情報」は、`props` で扱うことが多いです。

---

## 2. useState の概要

### 2-1. 目的

`useState` は、コンポーネント内で「状態」を宣言し、その値を更新するためのフックです。
状態が変わると、そのコンポーネントが自動的に再レンダリングされ、画面が最新の状態になります。

### 2-2. 基本構文

```jsx
const [state, setState] = useState(initialState);
```

* `state`
  現在の状態（値）を保持する変数
* `setState`
  状態を更新するための関数
* `initialState`
  コンポーネントの「初回表示時」に使われる初期値

ここでの `[]` は **配列の分割代入（destructuring）** という JavaScript の文法です。
`useState` が `[現在の値, 更新関数]` という2つの要素を返してくれるので、それをそれぞれ `state` と `setState` という名前で受け取っています。

```jsx
// よくある命名パターン
const [count, setCount] = useState(0);
const [name, setName] = useState('');
const [isOpen, setIsOpen] = useState(false);
```

### 2-3. 重要なポイント

* 状態を更新するときは、**必ず `setState` を使う**

  * `state = 1;` のように直接代入しても、React は変化に気づかず、画面が更新されません。
* `setState(newValue)` を呼ぶと、React が

  1. 新しい state を保存する
  2. コンポーネントを再レンダリングする
     という流れで動きます。

---

## 3. 基本的な使い方

### 3-1. 状態の宣言と更新

```jsx
// カウント（数値）を管理する状態を宣言
const [count, setCount] = useState(0);

// 状態の更新
setCount(count + 1);
```

* `count` には現在の値（初期は 0）が入る
* `setCount(新しい値)` を呼ぶと、次のレンダリングで `count` がその新しい値になる

### 3-2. 具体例：カウンターコンポーネント

```jsx
import React, { useState } from 'react';

function Counter() {
  // count という状態と、その更新関数 setCount を宣言（初期値は 0）
  const [count, setCount] = useState(0);

  return (
    <div>
      {/* 現在のカウント数を表示 */}
      <p>Count: {count}</p>

      {/* ボタンをクリックすると、count を 1 増やす */}
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

この例で起きていること：

1. 初回表示時、`count` は `0` でレンダリングされる
2. ボタンをクリックすると `setCount(count + 1)` が呼ばれる
3. React が `count` を更新し、`Counter` コンポーネントを再レンダリング
4. 画面上の `Count: 0` が `Count: 1` に変わる

---

## 4. よくある useState の利用ケース

ここからは、現場でもよく出てくる3つのパターンを見ていきます。

---

### ケース1: イベント引数を使って状態を更新する（フォーム入力）

#### 4-1-1. 用途

* テキストボックスの入力値
* チェックボックスの ON/OFF
* セレクトボックスの選択値

など、ユーザーの操作に応じて値を変えたい場合に使います。

#### 4-1-2. 実装例

```jsx
import React, { useState } from 'react';

const TextInput = () => {
  // name という文字列の状態を管理（初期値は空文字）
  const [name, setName] = useState('');

  // 入力が変わるたびに呼ばれるイベントハンドラ
  const handleName = (event) => {
    // event.target.value に、入力された文字列が入っている
    setName(event.target.value);
  };

  return (
    <div>
      <input
        type="text"
        value={name}       // 状態を表示に反映
        onChange={handleName} // 入力が変わるたびに状態を更新
      />
      <p>こんにちは、{name} さん</p>
    </div>
  );
};

export default TextInput;
```

#### 4-1-3. ポイント

* `value` 属性に state をバインドすると、**入力欄の表示 = state の値** という関係が保たれます（「制御されたコンポーネント」）。
* `onChange` でイベントを受け取り、`event.target.value` から入力値を取得して `setName` に渡します。

---

### ケース2: prevState（前の状態）を使った更新（カウンターなど）

#### 4-2-1. 用途

* カウンターの増減
* ページ番号の +1 / -1
* 前の値を元にした計算（例：`total + price`）

など、「**前の値を使って新しい値を計算する**」ときに使います。

#### 4-2-2. 実装例

```jsx
import React, { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0);

  const countUp = () => {
    // 前の状態 prevState に 1 を足した値で更新
    setCount((prevState) => prevState + 1);
  };

  const countDown = () => {
    setCount((prevState) => prevState - 1);
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

#### 4-2-3. なぜ `prevState` を使うのか？

`setCount(count + 1)` のように、**今の `count` を直接使った更新**でも動くように見えますが、

* 同じレンダリング内で `setCount` を複数回呼ぶ
* 他の処理と組み合わさる

といった場合に、値のズレが発生することがあります。

`setCount((prev) => prev + 1)` のように、「前回の値 `prev` から安全に計算する」書き方をしておくと、複数回の更新が重なっても正確な結果になります。

---

### ケース3: ON/OFF を切り替えるトグルボタン

#### 4-3-1. 用途

* モーダルの表示/非表示
* メニューの展開/折りたたみ
* スイッチの ON/OFF

など、Boolean（true / false）の状態を切り替える場面でよく使います。

#### 4-3-2. 実装例

```jsx
import React, { useState } from 'react';

const ToggleButton = () => {
  // open が true なら「開いている」、false なら「閉じている」
  const [open, setOpen] = useState(false);

  const toggle = () => {
    // 前の状態を反転させる
    setOpen((prevState) => !prevState);
  };

  return (
    <button onClick={toggle}>
      {open ? 'OPEN' : 'CLOSE'}
    </button>
  );
};

export default ToggleButton;
```

#### 4-3-3. ポイント

* `!prevState` で、`true` ↔ `false` をひっくり返しています。
* ボタンのラベルも `open` の値によって切り替えています。

---

## 5. もう一歩進んだ使い方：配列・オブジェクトの状態

実務では、「数値や文字列」だけでなく、**配列やオブジェクト**を state に持つことがよくあります。

### 5-1. NG 例：直接書き換えてしまう

```jsx
// NG: 直接変更しても React は変化に気づきにくい
const [user, setUser] = useState({ name: 'Taro', age: 20 });

user.age = 21;  // 直接代入（ミュータブルな書き換え）
setUser(user);  // うまく再レンダリングされないことがある
```

### 5-2. OK 例：新しいオブジェクトを作って渡す（イミュータブルに更新）

```jsx
const [user, setUser] = useState({ name: 'Taro', age: 20 });

const birthday = () => {
  setUser((prevUser) => ({
    ...prevUser,     // もともとのプロパティを展開
    age: prevUser.age + 1, // age だけ新しい値に
  }));
};
```

### 5-3. 配列の例

```jsx
const [items, setItems] = useState([]);

const addItem = (newItem) => {
  setItems((prevItems) => [...prevItems, newItem]);
};
```

* 配列も同様に、「直接 `push` する」のではなく、`...`（スプレッド構文）で新しい配列を作って渡します。

---

## 6. まとめ

* **useState とは？**

  * React コンポーネント内で、変化する値（状態 / state）を管理するための基本フック
  * `const [state, setState] = useState(initialState);` の形で使う

* **基本的な使い方**

  * `state` は現在の値、`setState` は更新用関数
  * 状態が変わるとコンポーネントが再レンダリングされ、画面が更新される
  * 直接代入ではなく、必ず `setState` を使う

* **よくあるパターン**

  * フォーム入力：`event.target.value` を使って状態更新
  * 前の値に基づいた更新：`setState(prev => prev + 1)`
  * トグル（ON/OFF）：`setState(prev => !prev)`

* **一歩進んだポイント**

  * 配列・オブジェクトを扱うときは、「新しい配列/オブジェクト」を作って渡す（イミュータブルに更新）
