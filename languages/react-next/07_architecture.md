# アーキテクチャ

## 概要

React / Next.jsにおけるアーキテクチャ設計は、

- 依存関係の整理
- 責務分離
- 再利用性
- フレームワーク依存の隔離

を目的とする。

---

## 基本思想

### 1. 依存方向を一方向にする

```txt
上位（具体）
↓
下位（抽象）
````

逆方向の依存は禁止する。

---

### 2. フレームワーク依存を隔離する

```txt
Next.js（pages）に依存する部分を分離
```

---

### 3. 画面とロジックを分離する

* 画面（UI）
* 業務ロジック
* データ取得

---

## ディレクトリ構成例

```txt
src/
├ pages
├ features/pages
├ shared-features
├ parts/
│  ├ primitives
│  ├ combinations
│  └ wrapped
```

---

## 各レイヤーの役割

---

### pages

```txt
Next.jsのルーティング層
```

役割：

* URLと画面の紐付け
* getServerSidePropsの定義
* featuresへの委譲

```tsx
export { default } from "@/features/pages/xxx/Page"
```

---

### features/pages

```txt
画面単位の実装
```

役割：

* ページコンポーネント
* ページ固有ロジック
* データ取得の入口

```txt
features/pages/user/
├ UserPage.tsx
├ contextToParams.ts
├ api.ts
└ types.ts
```

---

### shared-features

```txt
複数画面で共有する機能
```

例：

* 認証
* ユーザー管理
* 権限判定
* API共通処理

---

### parts

UIコンポーネント群

---

#### primitives

```txt
最小単位のUI
```

例：

* Button
* Input
* Text

---

#### combinations

```txt
複数UIの組み合わせ
```

例：

* Form
* Card
* Modal

---

#### wrapped

```txt
外部ライブラリのラッパー
```

例：

* MUI / Chakra UIのラップ
* カスタムスタイル適用

---

## 依存ルール（重要）

```txt
pages
 ↓
features/pages
 ↓
shared-features
 ↓
parts
```

---

### 禁止される依存

#### features/pages

```txt
@/pages に依存禁止
```

理由：

* ルーティング層に依存させない

---

#### shared-features

```txt
@/pages, @/features に依存禁止
```

理由：

* 共通ロジックが画面に依存すると再利用不可

---

#### parts

```txt
上位レイヤーに依存禁止
```

理由：

* UI部品は純粋であるべき

---

## 依存方向まとめ

```txt
OK
pages → features → shared → parts

NG
shared → features
features → pages
parts → features
```

---

## ページ構成例

```txt
pages/
└ users/[id].tsx

features/pages/users/
├ UserPage.tsx
├ contextToParams.ts
└ api.ts
```

---

### 処理の流れ

```txt
request
↓
pages（routing + SSR）
↓
features（props生成）
↓
UI描画
```

---

## contextToParamsの役割

```txt
Next.js context
↓
画面用propsへ変換
```

👉 フレームワーク依存を吸収

---

## なぜ分離するか

---

### 問題（分離しない場合）

```txt
pagesに全部書く
```

* ロジック肥大化
* テストしにくい
* 再利用不可
* Next.js依存が広がる

---

### 解決（分離）

```txt
pages = 薄く
features = 本体
```

---

## クリーンアーキテクチャとの関係

今回の構成は簡易的なレイヤード設計。

```txt
UI（pages / features）
↓
Application（features）
↓
Domain（shared-features）
↓
Infrastructure（apiなど）
```

---

## 実務での判断基準

* Next.js依存はpagesに閉じ込める
* 画面ロジックはfeaturesに置く
* 共通ロジックはsharedに置く
* UIはpartsに分離する

---

## アンチパターン

---

### pages肥大化

```txt
pagesに全部書く
```

---

### 逆依存

```txt
shared → features
```

---

### UIにロジックを書きすぎる

---

### 過剰分割

```txt
細かすぎるディレクトリ
```

---

## Vue（Nuxt）との違い

| Nuxt        | Next.js         |
| ----------- | --------------- |
| pagesに寄せがち  | 分離しやすい          |
| composables | hooks           |
| plugin      | shared-features |

---

## まとめ

* 依存方向の設計が最重要
* フレームワーク依存を隔離する
* featuresを中心に構成する
* 再利用性と保守性を高める構造にする
