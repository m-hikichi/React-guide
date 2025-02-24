# Reactの`useEffect`を理解しよう

Reactではコンポーネントがどのように動作するかを「ライフサイクル」と呼びます。コンポーネントが表示されたり更新されたり削除されたりするタイミングに合わせて、特定の処理を行いたい場合があります。これを実現するための仕組みが「副作用（effect）」です。今回は、`useEffect`フックを使って副作用を管理する方法を学びます。

## ライフサイクルとは？

ライフサイクルとは、コンポーネントが **生まれてから破棄されるまで** の時間の流れを指します。コンポーネントがマウント（表示）されたり、更新されたり、アンマウント（破棄）されたりするタイミングで処理を実行できます。

- **コンポーネントが表示されるタイミング**: `componentDidMount` (class components)
- **コンポーネントが更新されるタイミング**: `componentDidUpdate` (class components)
- **コンポーネントが破棄されるタイミング**: `componentWillUnmount` (class components)

React 16.8以降、Hooksが導入され、`useEffect`フックを使うことで関数コンポーネントでも同様のライフサイクル処理が可能になりました。

## 3種類のライフサイクル

Reactではコンポーネントのライフサイクルを次の3つの段階に分けて理解します。

1. **Mounting（マウント）**: コンポーネントが最初に表示されるタイミング
2. **Updating（更新）**: コンポーネントの状態やプロパティが変更されるタイミング
3. **Unmounting（アンマウント）**: コンポーネントが削除されるタイミング

これらのタイミングで副作用を実行するのが、`useEffect`の役割です。

---

## 副作用（Effect）フックを使おう

`useEffect`は、コンポーネントがレンダリングされた後に実行される「副作用」を管理するためのフックです。副作用とは、コンポーネント内で、状態の変更に伴って発生する非同期処理や外部のリソースの読み込みなど、Reactのレンダリングとは直接関係のない処理のことです。

例えば、APIからデータを取得する、イベントリスナーを設定する、外部データベースに接続するなどの処理が副作用にあたります。

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  const countUp = () => {
    setCount(prevState => prevState + 1);
  };

  const countDown = () => {
    setCount(prevState => prevState - 1);
  };

  // 毎回レンダリングされるたびに、countの値をログに出力
  useEffect(() => {
    console.log("Current count is...", count);
  });

  return (
    <div>
      <p>現在のカウント数: {count}</p>
      <button onClick={countUp}>up</button>
      <button onClick={countDown}>down</button>
    </div>
  );
};
```

上記のコードでは、`useEffect`でコンポーネントが再レンダリングされるたびに`count`の値をコンソールに出力します。

---

## `useEffect`の第二引数と依存関係

`useEffect`の第2引数には、依存関係（**deps**）という配列を渡すことができます。この依存関係によって、`useEffect`が実行されるタイミングを細かく制御することができます。

### 1. **毎回実行する**

引数を渡さない場合、`useEffect`は**毎回**実行されます。コンポーネントがレンダリングされるたびに、`useEffect`が実行されます。

```jsx
useEffect(() => {
  console.log("Current count is...", count);
});
```

### 2. **初回レンダリング後のみ実行する**

`useEffect`の第2引数に空の配列 `[]` を渡すと、**コンポーネントの初回レンダリング後のみ**実行されます。これによって、例えばコンポーネントが最初に表示された時だけ処理を実行したい場合に便利です。

```jsx
useEffect(() => {
  console.log("Current count is...", count);
}, []);
```

### 3. **特定の値が変更されたときに実行する**

配列の中に依存関係となる状態（`trigger` など）を渡すと、その状態が**変更されるたび**に`useEffect`が実行されます。たとえば、特定の状態が変わったときにAPI呼び出しを行うなどの処理に使います。

```jsx
useEffect(() => {
  console.log("Current count is...", count);
}, [count]); // `count`が変更されるたびに実行される
```

### 4. **複数の依存関係を指定する**

複数の状態が変更されたときに実行されるようにしたい場合、依存関係に複数の変数を渡すこともできます。

```jsx
useEffect(() => {
  console.log("Current count is...", count);
}, [count, trigger1, trigger2]);
```

これで、`count`、`trigger1`、`trigger2`が変更されるたびに`useEffect`が実行されます。

---

## クリーンアップ処理

副作用がある場合、その処理が終了した後に**クリーンアップ**を行うことが必要です。例えば、サーバーからデータを取得したり、外部のリソースに接続している場合、コンポーネントがアンマウントされる前にそれらを解除する必要があります。

`useEffect`内で返す関数を**クリーンアップ関数**と呼び、この関数はコンポーネントがアンマウントされる前や次のレンダリング前に実行されます。

```jsx
const ToggleButton = () => {
  const [open, setOpen] = useState(false);

  const toggle = () => {
    setOpen(prevState => !prevState);
  };

  useEffect(() => {
    console.log("Current state is", open);
    if (open) {
      console.log("Subscribe to database...");
    }

    // クリーンアップ関数を返す
    return () => {
      console.log("Unsubscribe from database!");
    };
  }, [open]);

  return (
    <button onClick={toggle}>
      {open ? "OPEN" : "CLOSE"}
    </button>
  );
};
```

この例では、`open`の状態が変更されるたびに`useEffect`が実行され、その後にクリーンアップ関数が呼ばれます。コンポーネントがアンマウントされる前に、外部リソースの接続を解除するのに役立ちます。

---

### まとめ

- `useEffect`は副作用を管理するためのフック
- 第2引数で依存関係を指定することで、`useEffect`が実行されるタイミングを制御できる
- クリーンアップ関数を使って、副作用の後片付けを行う
