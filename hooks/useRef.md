# React Hooks 入門：useRef

## 1. useRef とは？

`useRef` は、**コンポーネントのレンダリング間で値を保持するための「参照（ref）オブジェクト」を作るフック**です。

* `useState` との違い

  * `useState`：値を更新すると **再レンダリングが起こる**
  * `useRef`：`ref.current` を書き換えても **再レンダリングは起こらない**

つまり `useRef` は、

> 「画面を更新する必要はないけれど、コンポーネントが生きている間ずっと覚えておきたい値」

を保存しておくための「ミュータブルな箱（mutableな箱）」として使えます。

代表的な用途は次の 3 つです。

1. **DOM 要素へのアクセス**
   例：入力欄にフォーカスを当てる、スクロール位置を制御する
2. **レンダリング間で値を保持**
   例：前回の値、タイマー ID、フラグ（isMounted など）の保存
3. **パフォーマンス最適化**
   例：再レンダリングを起こしたくない内部的なカウンタやキャッシュ

---

## 2. 基本構文と「ref オブジェクト」

### 2-1. 基本構文

```jsx
const ref = useRef(initialValue);
```

* 戻り値 `ref` は **オブジェクト** で、常にこの形をしています：

```js
{
  current: initialValue // ここに自由に値を入れる
}
```

* この `ref` オブジェクト自体は、コンポーネントのライフサイクル中 **同じものが再利用** されます（毎回新しいオブジェクトを作るわけではない）。
* `ref.current` に代入しても、React は再レンダリングを行いません。

```jsx
const countRef = useRef(0);

countRef.current += 1;  // 値は変わるが、画面はそのまま（再レンダリングなし）
```

> 画面に反映させたい値 → `useState`
> 裏側で覚えておきたいだけの値 → `useRef`
> と使い分けるイメージです。

---

## 3. DOM 要素へのアクセス（もっとも典型的な useRef の使い方）

### 3-1. 入力欄にフォーカスを当てる例

```jsx
import React, { useRef, useEffect } from 'react';

function FocusInput() {
  const inputRef = useRef(null);

  const logInputValue = () => {
    if (!inputRef.current) return;
    console.log(inputRef.current.value); // 入力された文字列をログに出力
  };

  useEffect(() => {
    // 初回マウント時に自動でフォーカスを当てる
    if (inputRef.current) {
      inputRef.current.focus();
    }
  }, []);

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={logInputValue}>文字列をログに出力</button>
    </div>
  );
}

export default FocusInput;
```

ポイント：

* `<input ref={inputRef} />` のように `ref` 属性に `inputRef` を渡すと、

  * マウント時に `inputRef.current` に実際の DOM 要素が代入されます。
* 以降、`inputRef.current` から

  * `.focus()` でフォーカスを当てる
  * `.value` で値を読む／書く
    などの操作ができます。

### 3-2. DOM 用途での注意点

* React は基本的に「**DOM を直接いじらず、JSX で UI を宣言的に表現する**」ことを推奨しています。
* `useRef` を使って DOM を触るのは、

  * フォーカス制御
  * スクロール位置の調整
  * サードパーティ UI ライブラリとの連携（外部ライブラリが DOM を要求するケース）
    など「必要最小限」に留めるのがよいです。

---

## 4. レンダリング間で値を保持する（「前回の値」など）

`useRef` は、DOM だけでなく「**再レンダリングしても保持したい値**」を保存しておく箱としても使えます。

### 4-1. 前回のカウント値を保持する例

```jsx
import React, { useState, useEffect, useRef } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef(count); // 初期値として現在の count を入れておく

  useEffect(() => {
    // count が変わるたびに「前回の値」として保存
    prevCountRef.current = count;
  }, [count]);

  return (
    <div>
      <p>現在のカウント: {count}</p>
      <p>前回のカウント: {prevCountRef.current}</p>
      <button onClick={() => setCount((prev) => prev + 1)}>増加</button>
    </div>
  );
}

export default Counter;
```

動きのイメージ：

1. 初回表示時：`count = 0`, `prevCountRef.current = 0`
2. ボタンを押して `count` が 1 に更新 → 再レンダリング
3. `useEffect` が動き、`prevCountRef.current = 1` に更新
4. 次のレンダリングでは、「現在のカウント」と「前回のカウント」を見比べられる

ポイント：

* `prevCountRef.current` は書き換えても再レンダリングされないので、

  * `setCount` によるレンダリングごとに「前回の状態」を記録する用途に向いています。
* 同じパターンで「前回の props」「前回のフラグ値」なども保存できます。

---

## 5. パフォーマンス最適化・内部状態としての useRef

### 5-1. レンダリング回数を追跡する例

```jsx
import React, { useEffect, useRef } from 'react';

function RenderTracker() {
  const renderCount = useRef(0);

  useEffect(() => {
    // レンダリングが"完了"するたびにカウントアップ
    renderCount.current += 1;
    console.log('これまでのレンダリング回数:', renderCount.current);
  });

  return <p>このコンポーネントのレンダリング回数（完了ベース）はコンソールを確認してください。</p>;
}

export default RenderTracker;
```

ここでは、`renderCount` を **画面に表示する必要はないが、ログとして記録したい** だけなので `useRef` が適しています。

* `useState` を使ってしまうと、カウントアップ → 再レンダリング → カウントアップ… となり、不要なループやオーバーヘッドにつながります。
* `useRef` であれば `.current` を更新しても UI は再レンダリングされません。

### 5-2. 「再レンダリングさせたくないフラグ」の管理

例えば、コンポーネントがマウント済みかどうかを示すフラグなど、
「UI には関係ないけれど、ロジック上覚えておきたい値」は `useRef` で管理できます。

```jsx
const isMountedRef = useRef(false);

useEffect(() => {
  isMountedRef.current = true;
  return () => {
    isMountedRef.current = false;
  };
}, []);
```

この `isMountedRef.current` を使って、
非同期処理が完了したときに「まだコンポーネントが存在しているか？」を判定する、などの使い方ができます。

---

## 6. useRef を使うべき場面／使わない方がよい場面

### 6-1. useRef を使うべき場面

* DOM 要素を直接操作したい

  * フォーカス、スクロール、サイズの取得 など
* レンダリングに影響しない値を保存したい

  * タイマー ID
  * 前回の値（前回の props / state）
  * 完了フラグ（`isMounted` など）
* 再レンダリングなしでカウンタやキャッシュを管理したい

### 6-2. useRef を避けるべき場面

* 値の変化に応じて **UI を更新したいとき**

  * 例：カウンターの表示、フォームの入力値の表示
  * → この場合は `useState` / `useReducer` を使うべきです。

* コンポーネントの外部からも参照されるような「グローバル状態」

  * → `useContext` や状態管理ライブラリの方が適切な場合が多いです。

> 基本方針：
> 「UI に影響する状態」→ `useState` / `useReducer`
> 「UI に影響しない内部的なメモ」→ `useRef`

---

## 7. よくある落とし穴

### 7-1. useRef だけで状態管理をしようとする

```jsx
// ❌ NG: こう書くと UI は更新されない
const countRef = useRef(0);

const handleClick = () => {
  countRef.current += 1;
  // ここで画面に countRef.current を表示していても、自動では更新されない
};
```

`useRef` の更新では再レンダリングが発生しないため、
**画面に表示される情報を更新したい場合に useRef だけで完結させることはできません。**

* 画面表示にも使う値 → `useState` で管理
* 裏側でカウントするだけの値 → `useRef` で管理

のように役割を分けることが重要です。

---

### 7-2. ref を乱用すると React の「宣言的 UI」が崩れる

`useRef` を使うと、DOM を直接触ったり、非宣言的なロジックを書きやすくなります。

* 「とりあえず ref でなんとかする」を繰り返すと、

  * コンポーネントの挙動が読みづらくなる
  * React 本来の「状態 → UI」の流れが崩れる

ため、**本当に必要な箇所だけに絞る**のがベストプラクティスです。

---

## 8. まとめ

* **useRef とは**

  * コンポーネントのライフサイクル全体を通じて、
    「再レンダリングなしで値を保持できる」ミュータブルな箱（`{ current: ... }`）を作るフック

* **主な用途**

  1. DOM 要素へのアクセス（フォーカス、スクロール、サイズ取得など）
  2. レンダリング間で値を保持（前回の値、タイマー ID、フラグなど）
  3. パフォーマンス最適化（再レンダリングを起こしたくない内部カウンタ・キャッシュ）

* **使い分け**

  * UI に反映したい値 → `useState` / `useReducer`
  * UI には影響しない内部メモ → `useRef`

* **注意点**

  * `ref.current` を更新しても再レンダリングは起きない
  * useRef だけで状態管理を完結させようとしない
  * DOM 操作は必要最小限に留め、React の宣言的なスタイルを崩しすぎない
