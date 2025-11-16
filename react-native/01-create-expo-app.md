# create-expo-appでReact Native（Expo）環境構築

## 目次

1. [create-expo-appとは](#create-expo-appとは)
2. [React Native（Expo）環境構築に必要なもの](#react-nativeexpo環境構築に必要なもの)

   * 2.1 [Node.js](#nodejs)
   * 2.2 [npm / pnpm / yarn（パッケージマネージャ）](#npm--pnpm--yarnパッケージマネージャ)
   * 2.3 [端末・エミュレータ（Expo Go / Android Studio / Xcode）](#端末エミュレータexpo-go--android-studio--xcode)
3. [create-expo-appの実行手順](#create-expo-appの実行手順)
4. [プロジェクト構成の詳細](#プロジェクト構成の詳細)
5. [主要なスクリプトとその役割](#主要なスクリプトとその役割)
6. [ビルド・配布の基本（EAS Build）](#ビルド配布の基本eas-build)
7. [CRAとの主な違い（ざっくり比較）](#craとの主な違いざっくり比較)

---

## create-expo-appとは

`create-expo-app` は、**Expo（マネージドワークフロー）** で React Native アプリの開発をすぐに始められる公式ツールです。
ネイティブ開発環境の細かい設定（Xcode/Gradle）を意識せず、**JavaScript/TypeScript** と **Expo SDK** に集中できます。開発中は **Metro Bundler** を使い、実機は **Expo Go** アプリ、エミュレータは Android Studio / Xcode から起動できます。

---

## React Native（Expo）環境構築に必要なもの

### Node.js

* **推奨バージョン**：LTS（例：18系以上）
* **確認方法**

  ```bash
  node -v
  ```

### npm / pnpm / yarn（パッケージマネージャ）

* どれか1つでOK（本書では npm 前提）
* **確認方法**

  ```bash
  npm -v
  ```

### 端末・エミュレータ（Expo Go / Android Studio / Xcode）

* **実機**：iOS/Android に **Expo Go** アプリをインストールすれば、QRコードで即実行可能
* **エミュレータ**：

  * Android：**Android Studio**（AVD）
  * iOS：**Xcode**（iOS Simulator、macOSのみ）
* まずは実機 + Expo Go が最短です。ネイティブコードを触る場合のみ後述の「prebuild / run」へ。

---

## create-expo-appの実行手順

1. **プロジェクト作成**

   ```bash
   npx create-expo-app@latest <プロジェクト名>
   # 例: npx create-expo-app my-expo-app
   ```

2. **ディレクトリ移動**

   ```bash
   cd <プロジェクト名>
   ```

3. **開発サーバー起動（Metro Bundler）**

   ```bash
   npm start
   ```

   起動するとブラウザで **Expo DevTools** が開きます。
   ここから以下の実行ができます：

   * **i**：iOS Simulator（macOS）
   * **a**：Android Emulator
   * **w**：Web（ExpoのWebサポート）
   * **QRコード**をExpo Goで読み取り→実機で即時プレビュー

> ヒント：コマンドで直接起動も可能
>
> ```bash
> npx expo start --ios
> npx expo start --android
> npx expo start --web
> ```

---

## プロジェクト構成の詳細

`create-expo-app` で生成される主なファイル/ディレクトリ：

* **app／App.js / App.tsx**
  入口コンポーネント。最近のテンプレートでは `app/` ディレクトリと **Expo Router** が採用されることがあります。

  * `app/index.tsx`：ルート画面（Expo Router使用時）
  * `App.tsx`：単一エントリ（従来型テンプレート）

* **assets/**
  画像やアイコン、スプラッシュスクリーン素材など静的アセット。

* **package.json**
  依存関係とスクリプト定義。

* **app.json / app.config.(js|ts)**
  アプリ名、アイコン、スプラッシュ、バージョン、ネイティブ権限など **Expo 設定** を記述。

* **babel.config.js**
  Babel 設定（JS/TS を React Native 向けに変換）。

* **node_modules/**
  依存パッケージ一式。`npm install` により生成。

---

## 主要なスクリプトとその役割

* `npm start`
  **役割**：Metro Bundler（開発サーバー）を起動。ホットリロード/ファストリフレッシュで変更が即時反映。
  **補足**：`npx expo start --android/--ios/--web` のショートカットを `package.json` に定義している場合もあります。

* `npm run android`（存在するテンプレートの場合）
  **役割**：Android Emulator で起動（または実機ADB）。
  **注意**：ネイティブビルドではなく、Expo Go での実行が基本。

* `npm run ios`（macOSのみ・存在するテンプレの場合）
  **役割**：iOS Simulator で起動。

* `npm run web`
  **役割**：Web（React DOM）での実行。

* `npx expo prebuild`（必要時）
  **役割**：**マネージド → ネイティブ** プロジェクトを生成（iOS/Android ディレクトリ作成）。
  ライブラリでネイティブ設定が必要になった場合に実行します（旧「eject」に相当）。

* `npx expo run:android` / `npx expo run:ios`
  **役割**：prebuild 済みのネイティブプロジェクトをローカルでビルド＆実行。
  **注意**：マネージド運用だけなら不要。ネイティブ改変が必要な時のみ。

---

## ビルド・配布の基本（EAS Build）

本番配布用のアプリ（APK/AAB/IPA）を作る場合は **EAS Build** が推奨です。

1. **EAS CLI の導入**

   ```bash
   npm i -g eas-cli
   ```
2. **プロジェクトの初期化**

   ```bash
   eas build:configure
   ```
3. **プラットフォーム毎にビルド**

   ```bash
   eas build -p android
   eas build -p ios
   ```
4. 生成物は EAS のクラウドでビルドされ、URL から取得可能。
   （ローカルビルドが必要なら `eas build --local` もあります）

> Webデプロイは `npx expo export --platform web`（静的出力）等も利用できます。

---

## CRAとの主な違い（ざっくり比較）

| 項目       | CRA（create-react-app）   | Expo（create-expo-app）             |
| -------- | ----------------------- | --------------------------------- |
| 対象       | Web（React DOM）          | モバイル（iOS/Android）＋Web             |
| 開発サーバ    | Webpack Dev Server      | **Metro Bundler**（Expo DevTools）  |
| 実行       | ブラウザ                    | **Expo Go**（実機/エミュ） or Web        |
| 設定       | `react-scripts` に隠蔽     | **app.json/app.config** で多くを宣言的設定 |
| ネイティブコード | 基本不要                    | 基本不要（必要時 `prebuild` → ネイティブへ）     |
| 本番ビルド    | `npm run build`（静的ファイル） | **EAS Build**（AAB/IPA生成）          |
| ルーティング   | React Router 等          | Expo Router（`app/` ベース）推奨傾向       |

---

必要最低限はこれでOKです。
まずは **`npx create-expo-app` → `npm start` → Expo Go でQR読み取り** の最短ルートで動かしてみて、
その後に **EAS Build** や **prebuild**（ネイティブ化）が必要か検討するとスムーズです。
