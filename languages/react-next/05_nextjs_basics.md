# Next.js基礎

## 概要

Next.jsはReactのフレームワークであり、

- ルーティング
- サーバーサイドレンダリング（SSR）
- データ取得
- 最適化

などを提供する。

---

## Reactとの違い

| React | Next.js |
|---|---|
| 単体ライブラリ | フレームワーク |
| ルーティングなし | ファイルベースルーティング |
| CSR中心 | SSR / SSG対応 |
| 自由度高い | ある程度構造が決まっている |

---

## ルーティング（pages Router）

```txt
src/pages/
├ index.tsx       → /
├ users.tsx       → /users
└ users/[id].tsx  → /users/:id
````

* ファイル名がURLになる
* `[id]` は動的ルーティング

---

## ページの基本構造

```tsx
const Page = () => {
  return <div>Hello</div>
}

export default Page
```

---

## getServerSideProps（SSR）

```tsx
export const getServerSideProps = async (context) => {
  return {
    props: {}
  }
}
```

### 特徴

* リクエストごとにサーバーで実行
* SEOに強い
* 常に最新データ

---

## contextの中身

```ts
context.req
context.res
context.query
context.params
```

---

## 戻り値

```ts
return {
  props: {}
}
```

または：

```ts
return { notFound: true }
return { redirect: { destination: "/", permanent: false } }
```

---

## App Router（新しい方式）

```txt
src/app/
└ page.tsx
```

```tsx
export default async function Page() {
  const data = await fetch(...)
  return <div>{data}</div>
}
```

---

## Server Component（重要）

デフォルトはServer Component。

```tsx
export default async function Page() {
  const data = await fetch(...)
}
```

### 特徴

* サーバーで実行
* JSバンドルに含まれない
* 初期表示が高速

---

## Client Component

```tsx
"use client"

import { useState } from "react"

export default function Component() {
  const [count, setCount] = useState(0)
}
```

### 特徴

* ブラウザで実行
* state / eventが使える

---

## Server / Client の使い分け

### Server

* データ取得
* 初期表示
* SEO

---

### Client

* ボタン操作
* フォーム
* モーダル
* アニメーション

---

## 設計の基本

```txt
Server（デフォルト）
↓
必要な部分だけClient
```

---

## NGパターン

### 全部client

```tsx
"use client"
```

→ パフォーマンス悪化

---

### Serverで状態管理

```tsx
const [count, setCount] = useState(0)
```

→ 不可

---

## データ取得

### Server

```tsx
const data = await fetch(...)
```

---

### Client

```tsx
useEffect(() => {
  fetch(...)
}, [])
```

---

## SSR / SSG / ISR

### SSR（Server Side Rendering）

* 毎リクエストごとにHTML生成
* 常に最新データ
* 初回表示はやや遅い

---

### SSG（Static Site Generation）

* ビルド時にHTML生成
* 非常に高速
* データは固定

---

### ISR（Incremental Static Regeneration）

* 一定時間ごとに再生成
* SSGとSSRの中間

---

## SSR / SSG / ISR の使い分け（実務）

### SSRを使う

```txt
- ログインユーザーごとに内容が変わる
- リアルタイム性が必要
- 常に最新データが必要
```

例：

* マイページ
* ダッシュボード
* 管理画面

---

### SSGを使う

```txt
- 内容がほぼ変わらない
- SEOが重要
- パフォーマンス重視
```

例：

* LP（ランディングページ）
* 会社紹介
* ブログ記事

---

### ISRを使う

```txt
- 更新頻度はあるがリアルタイムでなくてよい
- SEOも重要
- パフォーマンスも重要
```

例：

* 商品一覧
* ニュース一覧
* メディア記事

---

## 判断フロー（実務用）

```txt
ユーザーごとに内容が変わる？
→ YES → SSR

NO ↓

更新頻度が低い？
→ YES → SSG

NO ↓

ISR
```

---

## Vue（Nuxt）との違い

| Nuxt          | Next.js                  |
| ------------- | ------------------------ |
| SSR中心         | Server/Client分離          |
| asyncData     | fetch / Server Component |
| ファイルベースルーティング | 同様                       |

---

## 実務での構成（重要）

今回のような構成：

```txt
pages/
└ routingのみ

features/pages/
└ 実装本体
```

### 意図

* Next.js依存を分離
* テストしやすい
* 可読性向上

---

## 例

```tsx
// pages
export { default } from "@/features/pages/xxx/Page"
```

---

## 判断基準

* ルーティング → pages
* 画面実装 → features
* 共通ロジック → shared

---

## アンチパターン

### pagesに全部書く

* ロジック肥大化
* 再利用不可

---

### Server / Clientを意識しない

* パフォーマンス低下
* バグの原因

---

## SEO観点の補足

### CSR（React単体）

```txt
HTMLが空
→ SEO弱い
```

---

### SSR / SSG（Next.js）

```txt
HTMLに内容あり
→ SEO強い
```

---

## まとめ

* Next.jsはReactの拡張
* ルーティングとSSRを提供
* Server / Clientの使い分けが重要
* SSR / SSG / ISRを用途で使い分ける
* 実務では構造設計が重要
