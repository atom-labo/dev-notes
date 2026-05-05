# データ取得（Data Fetching）

## 概要

Next.jsでは、データ取得の方法が複数ある。

- Server Component（推奨）
- getServerSideProps（pages Router）
- Client側（useEffect）
- データ取得ライブラリ（SWR / React Query）

👉 **どこで取得するか（Server or Client）が最重要**

---

## 全体整理

```txt
Serverで取得
├ Server Component（App Router）
└ getServerSideProps（pages Router）

Clientで取得
└ useEffect / SWR / React Query
````

---

## 1. Server Componentでの取得（推奨）

```tsx
export default async function Page() {
  const res = await fetch("https://api.example.com/users")
  const users = await res.json()

  return <div>{users.length}</div>
}
```

### 特徴

* サーバーで実行
* 初期HTMLにデータが含まれる（SEO強い）
* シンプル

---

## fetchの挙動（重要）

Next.jsの `fetch` はデフォルトでキャッシュされる。

```tsx
fetch(url, { cache: "force-cache" }) // デフォルト
```

---

### キャッシュ制御

```tsx
fetch(url, { cache: "no-store" }) // SSR（毎回取得）
```

```tsx
fetch(url, { next: { revalidate: 60 } }) // ISR（60秒ごと更新）
```

---

## 2. getServerSideProps（pages Router）

```tsx
export const getServerSideProps = async (context) => {
  const res = await fetch("https://api.example.com/users")
  const users = await res.json()

  return {
    props: { users }
  }
}
```

### 特徴

* リクエストごとに実行（SSR）
* contextが使える（req, resなど）
* pages Router専用

---

## 3. Clientでの取得（useEffect）

```jsx
const [users, setUsers] = useState([])

useEffect(() => {
  fetch("/api/users")
    .then(res => res.json())
    .then(setUsers)
}, [])
```

### 特徴

* 初期HTMLにはデータがない
* SEOに弱い
* ローディングが必要

---

## 4. SWR / React Query

### SWR

```jsx
import useSWR from "swr"

const { data, error } = useSWR("/api/users", fetcher)
```

---

### React Query

```jsx
const { data } = useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers
})
```

---

### 特徴

* キャッシュ管理
* 再取得（revalidate）
* ローディング管理
* エラーハンドリング

---

## Server vs Clientの判断

---

### Serverで取得する

```txt
- 初期表示に必要
- SEOが重要
- データ取得が重い
```

例：

* 記事ページ
* 商品ページ
* 一覧ページ

---

### Clientで取得する

```txt
- ユーザー操作後に取得
- リアルタイム更新
- SEO不要
```

例：

* フィルタリング
* 検索
* 無限スクロール
* ダッシュボード

---

## ハイブリッドパターン

```txt
Serverで初期データ取得
↓
Clientで追加取得
```

---

### 例

```tsx
// Server
const initialData = await fetch(...)

return <ClientComponent initialData={initialData} />
```

```jsx
"use client"

function ClientComponent({ initialData }) {
  const [data, setData] = useState(initialData)

  useEffect(() => {
    fetch(...)
  }, [])
}
```

---

## よくあるアンチパターン

---

### 初期データをClientで取得

```jsx
useEffect(() => {
  fetch(...)
}, [])
```

👉 SEO悪化
👉 初期表示遅い

---

### ServerとClientで二重取得

```txt
Serverで取得
↓
Clientでも再取得
```

👉 無駄な通信

---

### useEffect依存ミス

```jsx
useEffect(() => {
  fetchData()
}, [])
```

👉 本来更新すべきなのに更新されない

---

## Vue（Nuxt）との違い

| Nuxt      | Next.js          |
| --------- | ---------------- |
| asyncData | Server Component |
| useFetch  | fetch            |
| SSR自動     | 明示的に設計           |

---

## 実務での基本戦略

```txt
1. まずServerで取得
2. 必要ならClientで補完
```

---

## データ取得の責務分離

```txt
features/pages/
├ Page.tsx
├ api.ts
├ hooks.ts
└ types.ts
```

* API処理は分離
* コンポーネントに直接書かない

---

## まとめ

* データ取得は「どこでやるか」が最重要
* Serverが基本
* Clientは補助的に使う
* fetchのキャッシュ理解が重要
* ライブラリは必要に応じて導入
