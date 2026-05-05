# 実務パターン（Patterns）

## 概要

React / Next.jsの実務では、

```txt
「毎回考える」のではなく「パターンで組む」
````

ことが重要。

---

## 1. SSRページパターン

```txt
pages
↓
getServerSideProps
↓
api関数
↓
props生成
↓
Component描画
```

---

### 実装イメージ

```ts
export const getServerSideProps = async () => {
  const data = await fetchData()

  return {
    props: { data }
  }
}
```

```tsx
export const Page = ({ data }) => {
  return <Component data={data} />
}
```

---

### ポイント

* ComponentでAPIを呼ばない
* props駆動にする

---

## 2. Client初期化パターン（認証ページ）

```txt
Component
↓
usePageInitialization
↓
認証チェック
↓
API取得
↓
state更新
↓
描画
```

---

### 実装イメージ

```tsx
const { isLoading, isInitialized } = usePageInitialization(async () => {
  await getCurrentUser()
  const { data } = await getUserProfile()
  setState(data)
})
```

---

### ポイント

* 認証系はClientで処理
* 初期化処理を共通Hookにまとめる

---

## 3. API分離パターン

```txt
Component
↓
hooks
↓
api.ts
↓
axios
```

---

### api.ts

```ts
export const getUsers = async () =>
  httpClient.get("/users")
```

---

### ポイント

* UIからaxiosを呼ばない
* APIは関数化

---

## 4. axios共通化パターン

```txt
libs/Axios.ts
```

---

### パターン

```txt
httpClient
httpClientWithCredentials
```

---

### 用途

```txt
httpClient → 公開API
httpClientWithCredentials → 認証API
```

---

## 5. interceptorパターン

```txt
request → ヘッダー付与
response → エラー処理
```

---

### 例

```ts
httpClient.interceptors.request.use(...)
httpClient.interceptors.response.use(...)
```

---

## 6. Container / Presentational

```txt
Container（ロジック）
↓
Presentational（UI）
```

---

### 例

```tsx
// Container
const Page = () => {
  const data = useData()
  return <Component data={data} />
}

// UI
const Component = ({ data }) => {
  return <div>{data}</div>
}
```

---

## 7. カスタムフックパターン

```ts
export function useUsers() {
  const [data, setData] = useState([])

  useEffect(() => {
    fetchUsers().then(setData)
  }, [])

  return data
}
```

---

### 用途

* ロジックの再利用
* UIと分離

---

## 8. フォームパターン（React Hook Form）

```txt
useForm
↓
zodResolver
↓
schema
```

---

### 実装イメージ

```tsx
const form = useForm({
  resolver: zodResolver(schema)
})
```

---

## 9. ローディング制御

```txt
isLoading
isInitialized
```

---

### パターン

```tsx
if (isLoading && !isInitialized) return <Loading />
```

---

## 10. リダイレクト制御

```txt
router.push
router.replace
```

---

### パターン

```ts
if (!isAuthorized) {
  router.replace("/404")
}
```

---

## 11. データ変換パターン

```txt
APIレスポンス
↓
UI用データに変換
↓
Componentに渡す
```

---

### 例

```ts
const items = data.map(x => ({
  label: x.name,
  value: x.id
}))
```

---

## 12. SEO設定パターン

```txt
Component内でSEO設定
```

---

### 例

```tsx
<DummySeo
  title="..."
  description="..."
/>
```

---

## 13. ディレクトリ構成パターン

```txt
features/pages/
└ xxx/
  ├ Component.tsx
  ├ api.ts
  ├ hooks.ts
  ├ types.ts
```

---

## 14. 状態管理パターン

```txt
小規模
→ useState / props

中規模
→ useContext

大規模
→ 状態管理ライブラリ
```

---

## 15. アンチパターン

---

### UIにロジック詰め込み

```txt
Componentに全部書く
```

---

### axios直書き

```txt
useEffectで直接API
```

---

### SSR + Client二重取得

```txt
無駄な通信
```

---

### useEffect乱用

```txt
依存配列ミス
```

---

## まとめ

* 実務はパターンで組む
* SSR / Clientを使い分ける
* API層を分離する
* 共通処理はHookにまとめる
* UIは純粋に保つ
