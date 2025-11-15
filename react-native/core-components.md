# React Native コアコンポーネント入門

React Nativeでのアプリ開発は、**コアコンポーネント（Core Components）** と呼ばれる基本的なUI部品を組み合わせて行います。この記事では、アプリ開発で頻繁に使用される特に重要なコンポーネントを紹介します。

Web開発でHTMLタグ（`<div>`, `<p>`, `<img>`など）を使うように、React Nativeではこれらのコンポーネントを使って画面を構築します。大きな違いは、React NativeのコンポーネントがiOSとAndroidの**ネイティブUI部品に直接対応している**点です。これにより、アプリはネイティブアプリのような見た目とパフォーマンスを実現します。

## 目次
1. [基本的なコンポーネント](#基本的なコンポーネント)
   - [`<View>`](#view)
   - [`<Text>`](#text)
   - [`<Image>`](#image)
   - [`<ScrollView>`](#scrollview)
   - [`<TextInput>`](#textinput)
   - [`<StyleSheet>`](#stylesheet)
2. [その他の主要コンポーネントと学習リソース](#その他の主要コンポーネントと学習リソース)
3. [まとめ](#まとめ)

---

## 基本的なコンポーネント

### `<View>`
`<View>`は、UIを整理し、レイアウトを構築するための最も基本的なコンテナです。Web開発における`<div>`タグのような役割を果たします。他のコンポーネントを子要素として内包でき、スタイリング（特にFlexboxを使ったレイアウト）の基本単位となります。

```jsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const ViewExample = () => (
  <View style={styles.container}>
    <Text>最初のセクション</Text>
    <View style={styles.innerBox}>
      <Text>ネストされたView</Text>
    </View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: '#f0f0f0',
  },
  innerBox: {
    marginTop: 10,
    padding: 10,
    backgroundColor: 'skyblue',
  }
});

export default ViewExample;
```

### `<Text>`
`<Text>`は、文字列を表示するためのコンポーネントです。React Nativeでは、**テキストは必ず`<Text>`コンポーネントで囲む必要があります**。`<View>`の中に直接テキストを書くことはできません。

```jsx
import React from 'react';
import { Text, StyleSheet } from 'react-native';

const TextExample = () => (
  <Text style={styles.title}>
    これはタイトルです。
    <Text style={styles.nestedText}>（これはネストされたテキスト）</Text>
  </Text>
);

const styles = StyleSheet.create({
  title: {
    fontSize: 24,
    fontWeight: 'bold',
  },
  nestedText: {
    color: 'red',
    fontSize: 16,
  }
});

export default TextExample;
```

### `<Image>`
`<Image>`は、画像を表示するためのコンポーネントです。アプリ内のローカル画像と、ネットワーク経由の画像の両方を表示できます。

```jsx
import React from 'react';
import { View, Image, StyleSheet } from 'react-native';

const ImageExample = () => (
  <View style={styles.container}>
    {/* ローカル画像の表示 */}
    <Image
      source={require('./assets/local-image.png')}
      style={styles.image}
    />

    {/* ネットワーク画像の表示 */}
    <Image
      source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
      style={styles.image}
    />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  image: {
    width: 100,
    height: 100,
    margin: 10,
  }
});

export default ImageExample;
```

### `<ScrollView>`
`<ScrollView>`は、画面サイズに収まらないコンテンツをスクロール可能にするためのコンテナです。`View`と似ていますが、コンテンツが縦または横にはみ出した場合にスクロールバーが表示されます。

```jsx
import React from 'react';
import { ScrollView, Text, StyleSheet } from 'react-native';

const ScrollViewExample = () => (
  <ScrollView style={styles.container}>
    <Text style={styles.text}>スクロールできるコンテンツ</Text>
    <Text style={styles.text}>...</Text>
    <Text style={styles.text}>...</Text>
    {/* たくさんのコンテンツをここに追加 */}
  </ScrollView>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  text: {
    fontSize: 32,
    padding: 20,
    textAlign: 'center',
  }
});

export default ScrollViewExample;
```

### `<TextInput>`
`<TextInput>`は、ユーザーがテキストを入力するためのコンポーネントです。`useState`フックと組み合わせて、入力された値を管理するのが一般的です。

```jsx
import React, { useState } from 'react';
import { View, TextInput, Text, StyleSheet } from 'react-native';

const TextInputExample = () => {
  const [text, setText] = useState('');

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.input}
        placeholder="ここにテキストを入力..."
        onChangeText={newText => setText(newText)}
        defaultValue={text}
      />
      <Text style={styles.displayText}>入力されたテキスト: {text}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    padding: 20,
  },
  input: {
    height: 40,
    borderColor: 'gray',
    borderWidth: 1,
    paddingLeft: 8,
  },
  displayText: {
    marginTop: 10,
  }
});

export default TextInputExample;
```

### `<StyleSheet>`
`<StyleSheet>`は、コンポーネントのスタイルを定義するための仕組みです。必須ではありませんが、スタイル定義をJavaScriptオブジェクトとして一元管理し、パフォーマンスを最適化するメリットがあります。

`StyleSheet.create`メソッドを使うことで、スタイルオブジェクトが一度だけ作成され、複数の場所で再利用されるようになります。

```jsx
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 24,
    backgroundColor: '#eaeaea',
  },
  title: {
    marginTop: 16,
    paddingVertical: 8,
    borderWidth: 4,
    borderColor: '#20232a',
    borderRadius: 6,
    backgroundColor: '#61dafb',
    color: '#20232a',
    textAlign: 'center',
    fontSize: 30,
    fontWeight: 'bold',
  },
});
```

---

## Flexboxを使ったレイアウト
React Nativeのレイアウトは、**Flexbox** というアルゴリズムを基準にしています。Flexboxは、コンテナ内のアイテムの配置やサイズを柔軟に（flexibly）決めるための仕組みで、画面サイズが異なる多様なデバイスに対応する上で非常に強力です。

WebのCSS Flexboxとほぼ同じですが、いくつかの初期値が異なります。
- `flexDirection` の初期値は `column` です。（Webでは `row`）
- `flex` プロパティは、`flex: 1` のように単一の数値を指定します。

### 主なプロパティ
- **`flex`**: コンポーネントが利用可能なスペースをどのくらいの割合で占めるかを指定します。`flex: 1` を指定すると、親コンポーネントの利用可能なスペースをすべて占有します。
- **`flexDirection`**: アイテムを配置する主軸を決めます。
  - `row`: 横方向（左から右）に配置します。
  - `column`: 縦方向（上から下）に配置します。（デフォルト）
- **`justifyContent`**: 主軸方向のアイテムの配置方法を決めます。（`flex-start`, `center`, `flex-end`, `space-between`, `space-around`）
- **`alignItems`**: 交差軸方向（`flexDirection`が`column`なら横方向）のアイテムの配置方法を決めます。（`flex-start`, `center`, `flex-end`, `stretch`）

### レイアウト例
```jsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const FlexboxExample = () => (
  <View style={styles.container}>
    <View style={styles.header}>
      <Text style={styles.headerText}>Header</Text>
    </View>
    <View style={styles.main}>
      <View style={styles.box1} />
      <View style={styles.box2} />
      <View style={styles.box3} />
    </View>
    <View style={styles.footer}>
      <Text style={styles.footerText}>Footer</Text>
    </View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1, // 画面全体を占める
  },
  header: {
    height: 60,
    backgroundColor: 'tomato',
    justifyContent: 'center',
    alignItems: 'center',
  },
  headerText: { color: 'white', fontSize: 20 },
  main: {
    flex: 1, // HeaderとFooterの残りスペースをすべて占める
    flexDirection: 'row', // アイテムを横に並べる
    justifyContent: 'space-around', // 主軸（横）方向に均等に配置
    alignItems: 'center', // 交差軸（縦）方向の中央に配置
    backgroundColor: 'lightgray',
  },
  box1: { width: 60, height: 60, backgroundColor: 'skyblue' },
  box2: { width: 60, height: 60, backgroundColor: 'steelblue' },
  box3: { width: 60, height: 60, backgroundColor: 'darkblue' },
  footer: {
    height: 40,
    backgroundColor: 'dimgray',
    justifyContent: 'center',
    alignItems: 'center',
  },
  footerText: { color: 'white' },
});

export default FlexboxExample;
```

---

## タッチ操作の処理
React Nativeアプリをインタラクティブにするには、ユーザーのタッチ操作に応答する必要があります。そのために、いくつかのタッチ可能なコンポーネントが用意されています。

### `<Button>`
最もシンプルなタッチコンポーネントです。OS標準の見た目のボタンを表示し、`onPress`プロパティに関数を渡すことで、タップされたときの動作を定義できます。カスタマイズ性は低いですが、手軽に利用できます。

```jsx
import React from 'react';
import { View, Button, Alert, StyleSheet } from 'react-native';

const ButtonExample = () => (
  <View style={styles.container}>
    <Button
      title="ボタン"
      onPress={() => Alert.alert('ボタンが押されました！')}
      color="#841584"
    />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  }
});

export default ButtonExample;
```

### `<TouchableOpacity>` と `<Pressable>`
より柔軟なカスタマイズが必要な場合は、`TouchableOpacity`や`Pressable`を使います。これらのコンポーネントは、子要素として任意のコンポーネント（`<View>`, `<Text>`, `<Image>`など）を持つことができ、それ全体をタッチ可能にします。

- **`TouchableOpacity`**: ユーザーがタップした際に、コンポーネントが一瞬半透明になり、フィードバックを視覚的に示します。
- **`Pressable`** (推奨): より新しいコンポーネントで、`onPress`（タップ）、`onLongPress`（長押し）、`onPressIn`（押し始め）、`onPressOut`（離したとき）など、より詳細なインタラクションを検知できます。

#### `Pressable` の例
```jsx
import React, { useState } from 'react';
import { View, Pressable, Text, StyleSheet } from 'react-native';

const PressableExample = () => {
  const [timesPressed, setTimesPressed] = useState(0);

  return (
    <View style={styles.container}>
      <Pressable
        onPress={() => setTimesPressed((current) => current + 1)}
        style={({ pressed }) => [
          { backgroundColor: pressed ? 'rgb(210, 230, 255)' : 'white' },
          styles.wrapperCustom
        ]}>
        {({ pressed }) => (
          <Text style={styles.text}>
            {pressed ? '押されています！' : '押してください'}
          </Text>
        )}
      </Pressable>
      <View style={styles.logBox}>
        <Text>{timesPressed} 回押されました</Text>
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  wrapperCustom: {
    borderRadius: 8,
    padding: 6,
  },
  text: {
    fontSize: 16,
  },
  logBox: {
    padding: 20,
    margin: 10,
    borderWidth: StyleSheet.hairlineWidth,
    borderColor: '#f0f0f0',
    backgroundColor: '#f9f9f9',
  },
});

export default PressableExample;
```

---

## FlatListによるリスト表示
多くのアプリでは、SNSの投稿一覧や連絡先リストのように、大量のデータをスクロール可能なリストとして表示する必要があります。`<ScrollView>`は単純なスクロールを提供しますが、リストの項目が非常に多い場合、パフォーマンスが低下する可能性があります。

そこで使われるのが`<FlatList>`です。`<FlatList>`は、現在画面に見えているアイテムだけを描画し、スクロールに応じてアイテムを再利用することで、メモリ使用量を抑え、スムーズなスクロールを実現します。

### 主なプロパティ
- **`data`**: リストに表示するデータの配列。
- **`renderItem`**: 配列の各アイテムをどのように描画するかを定義する関数。
- **`keyExtractor`**: 各アイテムに一意のキーを指定する関数。Reactがアイテムを識別し、効率的に更新するために必要です。

### `FlatList` の例
```jsx
import React from 'react';
import { SafeAreaView, View, FlatList, StyleSheet, Text, StatusBar } from 'react-native';

const DATA = [
  { id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba', title: 'First Item' },
  { id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63', title: 'Second Item' },
  { id: '58694a0f-3da1-471f-bd96-145571e29d72', title: 'Third Item' },
];

const Item = ({ title }) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const App = () => {
  const renderItem = ({ item }) => (
    <Item title={item.title} />
  );

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={renderItem}
        keyExtractor={item => item.id}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

---

## 次のステップ

ここで紹介したコンポーネントと概念は、React Native開発の基礎です。これらを組み合わせることで、多くの基本的なアプリUIを構築できます。

さらに学習を進めるには、公式ドキュメントを参照し、他のコンポーネントやAPIについても学んでいくことをお勧めします。

- **[React Native 公式ドキュメント - Components and APIs](https://reactnative.dev/docs/components-and-apis)**

---

## まとめ

React Nativeのアプリ開発では、`<View>`でレイアウトを作り、`<Text>`で文字を表示し、`<Image>`で画像を配置するなど、基本的なコアコンポーネントを組み合わせてUIを構築します。

まずはこれらのコンポーネントの使い方に慣れることが、React Native学習の第一歩です。
