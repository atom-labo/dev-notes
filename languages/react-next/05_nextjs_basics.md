# Next.js 基本

## 概要

Next.jsはReactベースのフレームワークで、以下を提供する。

- SSR（Server Side Rendering）
- SSG（Static Site Generation）
- ルーティング
- APIルート
- パフォーマンス最適化

---

## ファイルベースルーティング

### 基本

```txt
pages/
├ index.tsx        → /
├ users.tsx        → /users
└ about.tsx        → /about
````

👉 ファイル構造 = URL

---

### 動的ルーティング

```txt
pages/users/[id].tsx
```

```txt
/users/123
/users/abc
```

---

### パラメータ取得

#### Client

```ts
const { query } = useRouter()
query.id
```

---

#### SSR

```ts
export const getServerSideProps = async (context) => {
  context.params.id
}
```

---

### 複数パラメータ

```txt
pages/users/[userId]/posts/[postId].tsx
```

---

### catch-all

```txt
pages/[...slug].tsx
```

```txt
/a/b/c → ["a", "b", "c"]
```

---

### 注意点

```txt
同階層に複数の動的ルートは置けない
例：[id].tsx と [name].tsx は競合する
```

---

## ページ遷移

### Link（推奨）

```tsx
import Link from "next/link"

<Link href="/users">Users</Link>
```

---

### router

```tsx
import { useRouter } from "next/router"

const router = useRouter()

router.push("/users")
```

---

### push / replace

```ts
router.push("/users")     // 履歴に残る
router.replace("/login")  // 履歴を上書き
```

---

### 使い分け

```txt
通常遷移 → push
リダイレクト → replace
```

---

## クエリパラメータ

### URL

```txt
/users?id=123
```

---

### 取得

```ts
router.query.id
```

---

### 遷移時

```ts
router.push({
  pathname: "/users",
  query: { id: 123 }
})
```

---

## routerの重要プロパティ

```ts
const router = useRouter()
```

---

### pathname

```ts
router.pathname
```

```txt
/users/[id]
```

---

### asPath

```ts
router.asPath
```

```txt
/users/123?id=1
```

---

### query

```ts
router.query
```

---

### isReady（重要）

```ts
if (!router.isReady) return
```

理由：

```txt
初回レンダリングではqueryが未確定
```

---

## SSR

### getServerSideProps

```ts
export const getServerSideProps = async (context) => {
  const data = await fetchData()

  return {
    props: { data }
  }
}
```

---

### 特徴

```txt
毎リクエスト実行
常に最新データ
SEOに強い
```

---

## SSG

### getStaticProps

```ts
export const getStaticProps = async () => {
  const data = await fetchData()

  return {
    props: { data }
  }
}
```

---

### 特徴

```txt
ビルド時に生成
高速
SEOに強い
```

---

## ISR

```ts
export const getStaticProps = async () => {
  return {
    props: { data },
    revalidate: 60
  }
}
```

---

### 特徴

```txt
一定時間で再生成
SSG + 更新性
```

---

## SSR / SSG / ISR 使い分け

```txt
SSR → 常に最新データ（一覧・検索）
SSG → ほぼ更新されない（LP・記事）
ISR → 更新頻度中（商品一覧など）
```

---

## Next.jsの役割

```txt
ルーティング
SSR / SSG
データ取得
パフォーマンス最適化
```

---

## Vueとの対応

| Vue        | Next.js            |
| ---------- | ------------------ |
| Vue Router | pages              |
| asyncData  | getServerSideProps |
| SSG        | getStaticProps     |

---

## まとめ

* ファイル構造でルーティングが決まる
* 動的ルートはファイル名で定義
* Link / routerで遷移
* SSR / SSG / ISRを使い分ける
* router.isReadyは実務で重要
