# React Hooks 入門：useContext

`useContext` フックは、React コンポーネント間で **共通の情報（コンテキスト）を共有するための仕組み**です。

例えば次のような「アプリ全体で共通して使いたい情報」を扱うときに役立ちます。

* テーマ（ライト/ダーク、色設定など）
* ログイン中のユーザー情報
* 選択中の言語（日本語/英語など）
* 権限情報（admin / user など）

本来であれば、親コンポーネントから子コンポーネントへ `props` を何段も渡していく必要があります（いわゆる **prop drilling**）。
`useContext` を使うと、「**ツリーの上で提供された値**」を、ツリーの深い下層から **直接読むことができる** ようになります。

---

## 1. コンテキストの概要と用語

React のコンテキストは、ざっくり言うと「**コンポーネントツリーの“上”から“下”に流れる共有値**」です。

### 1-1. 3つの構成要素

コンテキスト周りでは、次の 3 つの用語を押さえておくと理解しやすくなります。

1. **Context オブジェクト**

   * `React.createContext()` で作る「コンテキストそのもの」
   * `ThemeContext` や `UserContext` のように命名することが多い

2. **Provider コンポーネント**

   * `ThemeContext.Provider` のようなコンポーネント
   * `value` プロパティ経由で、「下のツリーに流す値」を指定する

3. **useContext フック**

   * `const value = useContext(ThemeContext);` のようにして
   * 「今、このコンポーネントから見える最新のコンテキストの値」を取得する

### 1-2. コンテキストのイメージ

コンポーネントツリーをざっくり図にすると：

```txt
<App>
  ├─ <ThemeContext.Provider value={...}>
  │    ├─ <Header>
  │    │    └─ <Title />  ← useContext(ThemeContext) でテーマを読む
  │    └─ <Content>
  │         └─ <ThemedButton />  ← 同じくテーマを読む
  └─ （この外側にはテーマは届かない）
```

`Provider` で囲んだ **内側のコンポーネントたちだけ** が、そのコンテキストの値を `useContext` で読むことができます。

---

## 2. useContext の基本的な使い方（3 ステップ）

ここでは、テーマ（背景色・文字色）をコンテキストで共有する例で説明します。

### ステップ 1：コンテキストの作成

```jsx
import React from 'react';

// ThemeContext という名前のコンテキストを作成
// createContext() の引数は「デフォルト値」
export const ThemeContext = React.createContext({
  background: 'white',
  color: 'black',
});
```

ポイント：

* `React.createContext(defaultValue)` で **Context オブジェクト**を作成
* `defaultValue` は「上位に Provider がない場合に使われる値」。
  通常は「安全なデフォルト」や「型のヒントとして使える値」を渡しておきます。

### ステップ 2：Provider で値を提供

```jsx
import React from 'react';
import { ThemeContext } from './ThemeContext';
import ThemedComponent from './ThemedComponent';

function App() {
  // ここでアプリ全体のテーマを決める
  const theme = {
    background: 'black',
    color: 'white',
  };

  return (
    // ThemeContext.Provider で子コンポーネントを囲み、value で値を提供
    <ThemeContext.Provider value={theme}>
      <ThemedComponent />
    </ThemeContext.Provider>
  );
}

export default App;
```

ポイント：

* `ThemeContext.Provider` の `value` に渡したオブジェクトが、ツリー内の子コンポーネントから参照されます。
* Provider で囲んでいないコンポーネントは、このテーマを受け取ることができません（default 値が使われる）。

### ステップ 3：useContext で値を取り出す

```jsx
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedComponent() {
  // 現在のテーマをコンテキストから取得
  const theme = useContext(ThemeContext);

  return (
    <div
      style={{
        background: theme.background,
        color: theme.color,
        padding: '20px',
      }}
    >
      Hello, World! この背景と文字色はテーマにより設定されています。
    </div>
  );
}

export default ThemedComponent;
```

ポイント：

* `useContext(ThemeContext)` で、「その時点で有効な ThemeContext の値」が返ってきます。
* `ThemeContext.Provider` の `value` が変更されると、`useContext(ThemeContext)` を使っているコンポーネントは自動的に再レンダリングされます。

---

## 3. コンテキストのスコープと default 値

### 3-1. Provider の内側だけが値を参照できる

```jsx
function App() {
  const theme = { background: 'black', color: 'white' };

  return (
    <>
      {/* ここには Provider がない → default 値が使われる */}
      <ThemedComponent />

      <ThemeContext.Provider value={theme}>
        {/* ここから下は Provider の内側 → theme が使われる */}
        <ThemedComponent />
      </ThemeContext.Provider>
    </>
  );
}
```

* 最初の `<ThemedComponent />` は、Provider の外なので `createContext` に指定したデフォルト値を使います。
* 2つ目は Provider の内側なので、`value={theme}` で指定した値を使います。

### 3-2. ネストした Provider で上書きできる

```jsx
function App() {
  const darkTheme = { background: 'black', color: 'white' };
  const lightTheme = { background: 'white', color: 'black' };

  return (
    <ThemeContext.Provider value={darkTheme}>
      {/* ここは darkTheme */}
      <Layout />

      <ThemeContext.Provider value={lightTheme}>
        {/* この内側は lightTheme に“上書き”される */}
        <SpecialSection />
      </ThemeContext.Provider>
    </ThemeContext.Provider>
  );
}
```

* 内側の Provider は、外側の Provider の値を「上書き」します。
* これにより、「アプリ全体はダークテーマだが、このセクションだけライトテーマ」といった構造を作ることができます。

---

## 4. useContext + useState / useReducer で「共有状態」を作る

`useContext` 自体は **「読むだけ」** のフックです。
実際に値を更新するには、`useState` や `useReducer` と組み合わせて使います。

### 4-1. 単純な状態共有のパターン

```jsx
// CounterContext.js
import React, { createContext, useState } from 'react';

export const CounterContext = createContext({
  count: 0,
  increment: () => {},
});

export function CounterProvider({ children }) {
  const [count, setCount] = useState(0);

  const increment = () => setCount((prev) => prev + 1);

  // 共有したい値と関数を value にまとめる
  const value = { count, increment };

  return (
    <CounterContext.Provider value={value}>
      {children}
    </CounterContext.Provider>
  );
}
```

```jsx
// App.js
import React from 'react';
import { CounterProvider } from './CounterContext';
import CounterDisplay from './CounterDisplay';
import CounterButton from './CounterButton';

function App() {
  return (
    <CounterProvider>
      <CounterDisplay />
      <CounterButton />
    </CounterProvider>
  );
}

export default App;
```

```jsx
// CounterDisplay.js
import React, { useContext } from 'react';
import { CounterContext } from './CounterContext';

function CounterDisplay() {
  const { count } = useContext(CounterContext);
  return <p>現在のカウント: {count}</p>;
}

export default CounterDisplay;
```

```jsx
// CounterButton.js
import React, { useContext } from 'react';
import { CounterContext } from './CounterContext';

function CounterButton() {
  const { increment } = useContext(CounterContext);
  return <button onClick={increment}>+1</button>;
}

export default CounterButton;
```

ポイント：

* `CounterProvider` の内部で `useState` を使って状態を管理
* `value` に「状態」と「更新関数」を一緒に渡すことで、
  どの子コンポーネントからでも `count` を読み書きできる

---

## 5. useContext を使うべき場面・避けるべき場面

### 5-1. 使うと便利な場面

* アプリ全体・大きな領域で共通の設定を扱いたいとき

  * テーマ（ライト/ダーク）
  * ロケール（言語）
  * 認証済みユーザー情報
  * 権限（ロール）
* 「どこからでも読みたい値」があるが、明らかに props で渡すと階層が深くなりすぎるとき

### 5-2. むやみに使うと複雑になる場面

* ローカルな状態（あるコンポーネントの中だけで完結する状態）
* 一時的な入力値・モーダルの開閉状態など、「ごく近い範囲の親子だけで完結するもの」

こういったものは、通常の `useState` と props 渡しで十分です。

> 基本方針としては、
> 「まず props でシンプルに作る → 本当に“どこからでも読みたい”状態だけ Context に切り出す」
> という順番で考えると、過剰な useContext 乱用を防げます。

---

## 6. よくある落とし穴とベストプラクティス

### 6-1. value に毎回新しいオブジェクトを渡してしまう

```jsx
// ❌ NG: 毎回「新しいオブジェクト」を value に渡している
<ThemeContext.Provider value={{ background: 'black', color: 'white' }}>
  {children}
</ThemeContext.Provider>
```

この書き方自体は動きますが、レンダリングのたびに `value` が新しいオブジェクトとして扱われるため、
`useContext(ThemeContext)` を使っているコンポーネントは **毎回再レンダリング** されます。

---

### 6-2. 「なんでもかんでも Context 化」しない

`useContext` は便利な一方で、多用すると「どこで値が変わっているかわからない」状態になります。

* 小さいスコープの state（ボタン 1 個の ON/OFF）まで全て Context に入れる
  → 逆に追いにくいコードになる
* コンポーネントの責務が不明確になる

**目安**

* まずは `useState` + props で始める
* 同じ props を 3 段・4 段と深く渡すようになってきたら、Context 化を検討する

---

## 7. まとめ

* **useContext とは**
  コンポーネントツリーの中で「共通の情報（コンテキスト）」を共有するためのフック。
  prop drilling を減らし、テーマ・ユーザー情報などをまとめて扱える。

* **基本の流れ（3 ステップ）**

  1. `React.createContext(defaultValue)` でコンテキストを作る
  2. `Context.Provider` の `value` でツリーに値を流す
  3. 子コンポーネントで `useContext(Context)` を使って値を読む

* **スコープと default 値**

  * Provider の内側だけが `value` にアクセス可能
  * Provider がない場合は `createContext` のデフォルト値が返る
  * Provider をネストすることで一部領域だけ別の値に上書きできる

* **状態共有パターン**

  * Provider 内で `useState` / `useReducer` を使い、「状態 + 更新関数」を context 経由で公開すると、複数コンポーネントで状態を共有できる

* **注意点**

  * value に毎回新しいオブジェクトを渡すと、全コンシューマが毎回再レンダリングされる
  * 小さいスコープの状態まで何でも Context に入れない
