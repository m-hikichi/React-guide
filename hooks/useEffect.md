# React Hooks 入門：useEffect

React コンポーネントは、画面（UI）の描画だけではなく、

* API からのデータ取得
* タイマー処理（`setInterval` / `setTimeout`）
* イベントリスナーの登録（`window.addEventListener`）
* コンソールへのログ出力
* `document.title` の変更

など、**「レンダリング以外の処理」＝ 副作用（side effect）** も行う必要があります。

`useEffect` フックは、この「副作用」をコンポーネントのライフサイクルに合わせて安全に実行・後片付けするための仕組みです。

---

## 1. 副作用とは何か

React が理想とするコンポーネント関数は、

> 「**同じ props と state を渡せば、常に同じ UI（JSX）を返すだけの関数**」

です。
しかし実際のアプリでは、次のような「外の世界に影響を与える処理」が必要になります。

* ネットワーク通信（fetch / axios）
* ブラウザ API の利用（`localStorage`、`document`、`window` など）
* タイマーやイベントリスナー
* ログ送信 など

これらをまとめて **副作用（side effects）** と呼んで、

* レンダリング本体（JSX を返す処理）から切り離して
* レンダリング「後」に実行する

ための場所が `useEffect` です。

---

## 2. コンポーネントのライフサイクルと useEffect

React のコンポーネントは、大きく分けて次の 3 段階で動きます。

* **マウント（Mount）**
  コンポーネントが初めて画面に現れる
* **更新（Update）**
  state や props が変わり、再レンダリングされる
* **アンマウント（Unmount）**
  コンポーネントが画面から消える

クラスコンポーネント時代は、

* `componentDidMount`
* `componentDidUpdate`
* `componentWillUnmount`

などのメソッドでこのタイミングに合わせて処理を書いていました。

`useEffect` を使うと、**関数コンポーネントでも同じことができる**ようになります。

---

## 3. useEffect の基本構文

### 3-1. 基本形

```jsx
useEffect(() => {
  // 副作用の処理を書く場所
  // 例: console.log, fetch, イベントリスナーの登録 など

  // 必要に応じてクリーンアップ関数を返す
  return () => {
    // 登録したタイマーやイベントリスナーの解除など
  };
}, 依存配列);
```

* 第 1 引数：**副作用の処理**（effect 関数）
* 第 2 引数：**依存配列（dependency array）** … 「どの値が変わったときに実行するか」を指定（後述）

### 3-2. 簡単な例

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // 副作用：count の値をコンソールに出力
    console.log('Current count is...', count);

    // クリーンアップ関数（今回はログだけなので必須ではない）
    return () => {
      console.log('Cleanup for count:', count);
    };
  });

  return (
    <div>
      <p>現在のカウント数: {count}</p>
      <button onClick={() => setCount((prev) => prev + 1)}>Increment</button>
    </div>
  );
}

export default Example;
```

この例だと、

* **コンポーネントがレンダリングされるたびに** `useEffect` の中身が実行されます。
* そして「次の副作用が実行される直前」や「コンポーネントが消える直前」に、クリーンアップ関数が呼ばれます。

---

## 4. 依存配列（dependency array）と実行タイミング

`useEffect` の第 2 引数に渡す **依存配列** を使うと、副作用が実行されるタイミングをコントロールできます。

```jsx
useEffect(() => {
  // effect 本体
}, [依存する値1, 依存する値2, ...]);
```

React は、依存配列の中の値を前回レンダリング時と比較し、**変化があったときだけ effect を実行**します。

---

### 4-1. 依存配列なし：毎回実行

```jsx
useEffect(() => {
  console.log('毎回実行される副作用');
});
```

* 初回マウント後
* その後の **すべての更新後**

で実行されます。

> クラスコンポーネントで言うと、`componentDidMount + componentDidUpdate` に近いイメージです。
> ※ただし `useEffect` は「DOM 更新が画面に反映された後」に呼ばれます。

---

### 4-2. 空配列 `[]`：初回レンダリング後のみ実行

```jsx
useEffect(() => {
  console.log('初回レンダリング後のみ実行');
}, []);
```

* **マウント時（初回表示直後）に 1 回だけ** effect が実行されます。
* その後、state や props が変わっても再実行されません。

典型的な用途：

* 初期ロード時の API 呼び出し
* 一度だけ行えばよい初期化処理（外部ライブラリの初期化など）

> クラスコンポーネントでいう `componentDidMount` 相当です。

---

### 4-3. 特定の値に反応：指定した値が変わったときだけ実行

```jsx
useEffect(() => {
  console.log(`カウントが更新されました: ${count}`);
}, [count]);
```

* 初回マウント時
* `count` の値が変わったとき

に effect が実行されます。

複数の依存関係を指定することもできます。

```jsx
useEffect(() => {
  console.log('count または anotherValue が変わりました');
}, [count, anotherValue]);
```

> 指定したどれか 1 つでも「前回と異なる値」になれば effect が実行されます。

---

### 4-4. 依存配列とライフサイクルの対応表

ざっくりまとめると次のようになります。

| 記述                       | マウント | 更新（依存値変更）     | アンマウント前のクリーンアップ |
| ------------------------ | ---- | ------------- | --------------- |
| `useEffect(fn)`          | 実行   | 毎回実行          | 毎回 + アンマウント時    |
| `useEffect(fn, [])`      | 実行   | 実行されない        | アンマウント時         |
| `useEffect(fn, [value])` | 実行   | `value` 変更時のみ | 次の実行前 + アンマウント時 |

---

## 5. クリーンアップ処理（後片付け）

副作用によっては、「そのまま放置するとまずい」ものがあります。

* `setInterval` / `setTimeout` のタイマー
* `window.addEventListener` で登録したイベントリスナー
* WebSocket / 通信の購読

これらは、**コンポーネントが不要になったときに解除しないと**、

* ゴミが溜まり続ける（メモリリーク）
* 画面から消えたコンポーネントがまだイベントを受け取り続ける

という問題が起こります。

そのため、`useEffect` では副作用を実行したあとに、**クリーンアップ関数**を返すことでここを解決します。

---

### 5-1. タイマーの例

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log('毎秒実行');
  }, 1000);

  // クリーンアップ：タイマーの解除
  return () => {
    clearInterval(timer);
  };
}, []); // 初回マウント時のみ登録
```

* マウント時に `setInterval` を登録
* アンマウント時に `clearInterval` で解除

> `[]` を付けておかないと、再レンダリングのたびに新しいタイマーが増え続けてしまうので注意が必要です。

---

### 5-2. イベントリスナーの例

```jsx
useEffect(() => {
  const handleResize = () => {
    console.log('window resized');
  };

  window.addEventListener('resize', handleResize);

  // クリーンアップ：イベントリスナーの解除
  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, []); // 初回マウント時にだけ登録
```

---

### 5-3. クリーンアップのタイミング

React は、次のタイミングでクリーンアップ関数を呼びます。

1. **依存配列の値が変わって、次の effect を実行する直前**
2. **コンポーネントがアンマウントされる直前**

イメージとしては、

1. 前回の effect の後片付け（クリーンアップ）
2. 新しい effect を実行

という順番です。

---

## 6. 初心者がハマりやすいポイント

### 6-1. 依存配列の指定ミスによる「無限ループ」

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  setCount((prev) => prev + 1);
}, []); // ← これは OK（初回一度だけ）

// もしこう書くと…
useEffect(() => {
  setCount((prev) => prev + 1);
}); // ← 依存配列なしは NG
```

依存配列なしで、effect 内で state を更新すると、

1. レンダリング → effect 実行 → `setCount` で state 更新
2. state 更新 → レンダリング → effect 実行 → また `setCount`
3. これが繰り返されて無限ループ

といった状態になります。

**対応策**

* 「いつ effect を実行したいのか」を考えて、適切な依存配列を指定する
* 何となく依存配列を空にしたり、逆に何も指定しないのは避ける

---

### 6-2. 依存配列に「必要な値」を入れ忘れる

```jsx
useEffect(() => {
  // count を使っているのに…
  console.log(count);
}, []); // ← 依存配列が空
```

この場合、`count` が変わっても effect が再実行されず、「常に初回の count だけを見る」effect になります（いわゆる「古い値（stale）」問題）。

**基本ルール**

> **effect の中で参照している値（state / props / 関数）は、原則として依存配列に入れる**

実務では、`eslint-plugin-react-hooks` の `exhaustive-deps` ルールを有効にしておくと、
依存配列漏れを警告してくれるので、それに従って修正するのが安全です。

---

### 6-3. async 関数を直接 useEffect に渡さない

```jsx
// ❌ NG: async を直接付ける
useEffect(async () => {
  const res = await fetch('/api/data');
  // ...
}, []);
```

`useEffect` に渡す関数は「クリーンアップ関数を返すかもしれない」ことが前提なので、
`async` をつけると「暗黙に Promise を返す関数」になり、意図しない挙動の原因になります。

**対応パターン**

```jsx
useEffect(() => {
  const fetchData = async () => {
    const res = await fetch('/api/data');
    const data = await res.json();
    // setState など
  };

  fetchData();
}, []);
```

* `useEffect` に渡す関数自体は同期関数にして
* 中で `async` な関数を定義して呼び出す

という形にしておくと安全です。

---

## 7. useEffect を使う・使わないの判断

最後に、実務でよくある迷いどころです。

> 「これ、`useEffect` に書くべき？
> それとも普通にレンダリング中に計算すればいい？」

という判断が必要になります。

目安としては：

* **使うべきケース**

  * 外部の世界とやり取りするとき

    * API コール
    * ブラウザ API（`window` / `document`）
    * タイマー
    * イベントリスナー
  * React の外側に影響を与える処理（ログ送信など）

* **使わない方がよいケース**

  * state と props だけから **純粋に計算できる値**

    * 例：`const doubled = count * 2;`
  * ただの変換・フィルタリング・ソートなどの「計算系」ロジック

> 「**計算で済むものはレンダリング中にそのまま書き、
> 外部とのやり取りは useEffect に閉じ込める**」
> という意識を持っておくと、コードがシンプルになります。

---

## 8. まとめ

* `useEffect` は、React コンポーネントの「副作用（レンダリング以外の処理）」を扱うためのフック
* 依存配列を使って、「いつ effect を実行するか」をコントロールできる

  * 指定なし：毎回実行
  * `[]`：初回だけ
  * `[count]`：`count` が変わるたびに実行
* effect から関数を返すと、それがクリーンアップ処理として

  * 次の effect 実行前
  * アンマウント前
    に呼び出される
* 初心者が特につまずきやすいポイント

  * 依存配列なし + state 更新 → 無限ループ
  * 依存配列の書き忘れ → 古い値を見続ける
  * async を直接渡す → Promise を返す effect になってしまう
* 「外部とのやり取りは useEffect」「単なる計算はレンダリング中」という切り分けを意識する
