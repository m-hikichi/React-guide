# React Hooks 入門：useMemo

## 1. useMemo とは？

`useMemo` は、**「計算結果をメモ化（memoization）」して再利用するためのフック**です。

* 重い計算（フィルタリング・ソート・集計など）を毎回実行したくない
* 子コンポーネントに渡すオブジェクトや配列の **参照（identity）を安定させたい**
* 依存する値が変わっていないときは「前回の結果をそのまま使いたい」

といった場面で使います。

ざっくりまとめると：

> 「**値を計算する関数**を渡すと、
> 依存している値が変わらない限り、前回計算した結果を再利用してくれるフック」

です。

### 1-1. useMemo と他の Hooks との違い

* `useState`
  → 「状態（state）」を保持するためのフック。更新すると再レンダリングが起こる。
* `useEffect`
  → レンダリング後に「副作用」を実行するためのフック。
* `useRef`
  → 再レンダリングなしで値を保持するためのフック。
* `useMemo`
  → 「**計算された値**」をメモ化して再利用するフック。

`useMemo` は「**値**」を返す点が特徴です。
（`useCallback` は「関数」をメモ化するフックで、`useMemo(() => fn, deps)` に近いイメージです。）

---

## 2. 基本構文

```jsx
const memoizedValue = useMemo(
  () => {
    // 重い計算処理
    return 計算結果;
  },
  [依存する値1, 依存する値2]
);
```

* 第 1 引数：値を計算する関数（**副作用を書かない**純粋な計算にする）
* 第 2 引数：依存配列
  → この配列の中の値が変わったときだけ、関数を再実行して新しい値を計算します。

ポイント：

* 初回レンダリング時に計算関数が実行され、結果が保存される
* 2 回目以降のレンダリングでは、

  * 依存配列の中の値が変わっていれば → 再計算
  * 変わっていなければ → 前回の結果をそのまま返す

---

## 3. よくある利用パターン 1：重い計算の結果をメモ化する

### 3-1. フィルタリング・ソートの例

```jsx
import React, { useState, useMemo } from 'react';

function UserList({ users }) {
  const [keyword, setKeyword] = useState('');

  // keyword と users が変わったときだけ、フィルタリングをやり直す
  const filteredUsers = useMemo(() => {
    console.log('フィルタリング実行');
    return users.filter((user) =>
      user.name.toLowerCase().includes(keyword.toLowerCase())
    );
  }, [users, keyword]);

  return (
    <div>
      <input
        type="text"
        placeholder="名前で検索"
        value={keyword}
        onChange={(e) => setKeyword(e.target.value)}
      />
      <ul>
        {filteredUsers.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

ポイント：

* `users.filter(...)` が重い処理であっても、

  * `users` と `keyword` が変わらない限り、前回の `filteredUsers` を再利用する
* 入力欄の変更や他の state 更新でレンダリングが起きても、
  依存値が変わらなければフィルタリングは走らない

### 3-2. どんな計算を useMemo で包むべきか？

* 例えば次のような処理は、`useMemo` の候補になり得ます。

  * 大きな配列のフィルタ・ソート・集計
  * 重い計算（グラフ描画用のデータ変換など）
  * 同じ結果を何度も計算するコストが高いもの

逆に、**単純な足し算・文字列結合程度**であれば、`useMemo` を使う必要は基本的にありません（オーバーヘッドの方が大きくなる可能性があるため）。

---

## 4. よくある利用パターン 2：オブジェクト・配列の参照を安定させる

`useMemo` は、**子コンポーネントに渡す「オブジェクト/配列の props」の参照（identity）を安定させる**目的でもよく使われます。

### 4-1. なぜ参照の安定が必要になるのか？

JavaScript では、オブジェクトや配列は「中身が同じでも、毎回新しいインスタンスを作ると `===` が false」になります。

```js
{} === {}         // false
[1, 2] === [1, 2] // false
```

React の `memo` や `useEffect` の依存配列で「**前回と同じかどうか**」を判定する際は `===` による参照比較が使われるため、

* 見た目は同じでも毎回新しい `{}` を作っていると「毎回違う値」と判定される
* それが原因で

  * 子コンポーネントが毎回再レンダリングされる
  * `useEffect` が毎回走ってしまう

といった問題が起こります。

### 4-2. useMemo でオブジェクトをメモ化する例

```jsx
import React, { useState, useMemo } from 'react';
import Child from './Child';

function Parent() {
  const [count, setCount] = useState(0);

  // ❌ NG: 毎回新しいオブジェクトが生成される
  // const config = { color: 'red' };

  // ✅ OK: count が変わらない限り同じ参照を再利用
  const config = useMemo(() => {
    return { color: 'red' };
  }, []); // 今回は特に依存がないので []

  return (
    <div>
      <button onClick={() => setCount((prev) => prev + 1)}>
        親のカウンタ: {count}
      </button>
      <Child config={config} />
    </div>
  );
}

export default Parent;
```

```jsx
// Child.js
import React, { memo } from 'react';

const Child = memo(function Child({ config }) {
  console.log('Child レンダリング');
  return <p style={{ color: config.color }}>子コンポーネント</p>;
});

export default Child;
```

* `Child` は `memo` でラップされており、props が「=== で同じ」であれば再レンダリングをスキップします。
* `config` を `useMemo` でメモ化することで、

  * 親が再レンダリングされても `config` の参照が変わらない
  * 結果として子の再レンダリングも抑えられる

### 4-3. useMemo + useEffect の依存配列

`useEffect` の依存としてオブジェクトや配列を指定する場合も、`useMemo` で参照を安定させておくと、無駄な再実行を防げます。

```jsx
const options = useMemo(
  () => ({
    page,
    pageSize,
  }),
  [page, pageSize]
);

useEffect(() => {
  fetchData(options);
}, [options]); // options 自身は useMemo で安定している
```

---

## 5. 依存配列とライフサイクル

`useMemo` の依存配列は、`useEffect` とほぼ同じ考え方です。

```jsx
const memoizedValue = useMemo(
  () => computeSomething(a, b),
  [a, b]
);
```

この場合：

* 初回マウント時に `computeSomething(a, b)` が実行され、`memoizedValue` に結果が保存される
* 次回以降のレンダリングでは、

  * `a` または `b` のどちらかが変わっていれば → `computeSomething` を再実行して新しい結果を返す
  * 両方とも前回と同じなら → 前回計算した `memoizedValue` をそのまま返す

### 5-1. useEffect との違い（あくまで「値の計算」専用）

* `useEffect` は「レンダリング後に実行される副作用用フック」
* `useMemo` は「レンダリング中に *使う* 値を事前に計算してメモ化するためのフック」

**副作用（API 呼び出し・ログ出力など）を `useMemo` の中に書くのはアンチパターン**です。
副作用は `useEffect` に寄せるべきです。

---

## 6. useMemo と useCallback の関係

`useCallback` は、**関数用の useMemo** と考えることができます。

```jsx
// useCallback の定義イメージ
useCallback(fn, deps) ≒ useMemo(() => fn, deps);
```

* `useMemo`
  → `() => 計算結果` を渡し、「値」を返す
* `useCallback`
  → `fn` を渡し、「関数」を返す（`fn` の参照をメモ化する）

子コンポーネントに「コールバック関数」を渡すときなどは、`useCallback` を使った方が意図が明確になります。

---

## 7. 使うべき場面／使うべきでない場面

### 7-1. useMemo を使うと良い場面

* 明らかに **計算コストが高い処理** を毎回実行している

  * 重いフィルタ・ソート・集計
  * グラフ描画のための複雑なデータ変換
* 子コンポーネントに渡すオブジェクト/配列/関数の **参照を安定させたい**

  * `React.memo` / `useEffect` / `useCallback` と組み合わせる用途

### 7-2. 避けるべき・過剰最適化になりがちな場面

* ただの `a + b` や `count * 2` 程度の軽い計算
* 「なんとなく全部 useMemo しておけば速くなりそう」という発想

`useMemo` 自体にもオーバーヘッド（依存配列の比較・メモリ使用など）があるため、**すべての計算に使うと逆にパフォーマンスが悪化する可能性**があります。

> 目安：
> 「`useMemo` を外したときに体感できる遅さがあるか？」
> まずは素直に書いて、遅いと感じたり計測で問題が見えた部分だけ `useMemo` を導入する、という順番が推奨されます。

---

## 8. よくある落とし穴

### 8-1. useMemo の中で副作用を書く

```jsx
// ❌ NG 例：useMemo 内で fetch や console.log を行う
const data = useMemo(() => {
  console.log('これは副作用なので useEffect に書くべき');
  fetch('/api/data'); // ← これも副作用
  return someCalculation();
}, [deps]);
```

`useMemo` は「値の計算専用」です。
副作用（外部とのやり取り）は **必ず `useEffect` に書き、`useMemo` の中では純粋な計算だけ** にしてください。

---

### 8-2. 依存配列の書き忘れによる「古い値」

```jsx
const memoizedValue = useMemo(() => {
  // 実は count を参照している
  return heavyCalculation(count);
}, []); // ← 依存配列が空
```

この場合、初回マウント時に一度だけ計算され、その後 `count` が変わっても `memoizedValue` は更新されません。
`useEffect` と同じく、**計算の中で参照している値は基本的に依存配列に含める**必要があります。

ESLint の `react-hooks/exhaustive-deps` ルールを有効にしておくと、依存関係漏れを警告してくれるため、実務ではそれに従うのが安全です。

---

### 8-3. useMemo で「再レンダリングを止める」ことはできない

`useMemo` は **あくまで「計算結果の再利用」** です。

* コンポーネントの再レンダリング自体は、`useState` や親コンポーネントの再レンダリングなどによって起こります。
* `useMemo` は、そのレンダリングの中で「計算をサボる」ための仕組みであり、
  **再レンダリングそのものを止めるわけではありません。**

再レンダリングを抑えたいときは、

* 親コンポーネントの設計を見直す
* `React.memo` や `useCallback` と併用する
* 状態の持ち方を変える

など、別の観点での最適化も必要になります。

---

## 9. まとめ

* **useMemo とは**

  * 「値を計算する関数」と「依存配列」を渡すと、
    依存する値が変わらない限り、前回の計算結果を再利用してくれるフック。

* **主な用途**

  1. 重い計算（フィルタ・ソート・集計など）の結果をメモ化してパフォーマンスを改善
  2. 子コンポーネントに渡すオブジェクト/配列の参照を安定させて、無駄な再レンダリングを減らす

* **依存配列**

  * `useEffect` と同様に、計算で使っている値は基本的に依存配列に含める
  * 依存配列が変わったときだけ再計算される

* **注意点**

  * `useMemo` の中で副作用を書かない（副作用は `useEffect` に寄せる）
  * 軽い計算まで何でも `useMemo` するのは過剰最適化
  * `useMemo` は再レンダリングを止めるものではなく、「再計算を減らす」ためのもの
