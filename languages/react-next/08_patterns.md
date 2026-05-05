# 実務パターン（Patterns）

## 概要

React / Next.jsの実務では、よく使う「実装パターン」を持っているかが重要。

- 毎回ゼロから考えない
- 一貫した設計にする
- バグを減らす

---

## 1. データ取得パターン（基本）

### Serverで取得 → Clientで操作

```tsx
// Server
export default async function Page() {
  const data = await fetch(...)
  return <ClientComponent initialData={data} />
}
````

```jsx
"use client"

function ClientComponent({ initialData }) {
  const [data, setData] = useState(initialData)
}
```

---

### 判断基準

* 初期表示 → Server
* ユーザー操作 → Client

---

## 2. フォーム処理

### Controlled Component

```jsx
const [value, setValue] = useState("")

<input value={value} onChange={e => setValue(e.target.value)} />
```

---

### 送信処理

```jsx
const handleSubmit = (e) => {
  e.preventDefault()
  submit(value)
}
```

---

### 実務ポイント

* 入力値はstateで管理
* バリデーションは関数で分離
* API呼び出しは別関数へ

---

## 3. API呼び出しの分離

```txt
features/pages/user/
├ api.ts
├ hooks.ts
└ UserPage.tsx
```

---

### api.ts

```js
export async function fetchUsers() {
  const res = await fetch("/api/users")
  return res.json()
}
```

---

### hooks.ts

```js
export function useUsers() {
  const [users, setUsers] = useState([])

  useEffect(() => {
    fetchUsers().then(setUsers)
  }, [])

  return users
}
```

---

## 4. ローディング・エラー管理

```jsx
const [loading, setLoading] = useState(true)
const [error, setError] = useState(null)
```

---

```jsx
if (loading) return <Loading />
if (error) return <Error />
```

---

### SWR / React Queryを使う場合

```jsx
const { data, error, isLoading } = useSWR(...)
```

👉 状態管理が簡潔になる

---

## 5. モーダル管理

### パターン

```jsx
const [isOpen, setIsOpen] = useState(false)
```

---

```jsx
{isOpen && <Modal onClose={() => setIsOpen(false)} />}
```

---

### ポイント

* 表示状態は親が管理
* Modalは表示だけ担当

---

## 6. リスト表示

```jsx
{items.map(item => (
  <Item key={item.id} item={item} />
))}
```

---

### 注意

* `key` は必須
* indexは基本使わない

---

## 7. フィルタ・検索

```jsx
const filtered = useMemo(() => {
  return items.filter(item => item.name.includes(keyword))
}, [items, keyword])
```

---

### ポイント

* filterはuseMemoで最適化
* 入力はuseStateで管理

---

## 8. イベントハンドラ

```jsx
<button onClick={handleClick} />
```

---

### 引数付き

```jsx
<button onClick={() => handleClick(id)} />
```

---

### NG

```jsx
<button onClick={handleClick()} />
```

---

## 9. 状態の持ち上げ

```txt
Parent
 ├ ChildA
 └ ChildB
```

→ Parentにstateを置く

---

## 10. Contextの利用

```jsx
const UserContext = createContext(null)
```

---

```jsx
<UserContext.Provider value={user}>
  <App />
</UserContext.Provider>
```

---

### 用途

* propsドリル回避
* グローバル状態

---

## 11. カスタムフックパターン

```jsx
function useFetch(url) {
  const [data, setData] = useState(null)

  useEffect(() => {
    fetch(url).then(res => res.json()).then(setData)
  }, [url])

  return data
}
```

---

## 12. レイアウトパターン

```jsx
function Layout({ children }) {
  return (
    <div>
      <Header />
      {children}
    </div>
  )
}
```

---

## 13. ページ構成（実務）

```txt
pages/
└ routingのみ

features/pages/
└ 実装本体
```

---

## 14. ハイブリッドデータ取得

```txt
Server（初期データ）
↓
Client（追加取得）
```

---

## 15. パフォーマンスパターン

```jsx
const data = useMemo(() => compute(items), [items])
const handleClick = useCallback(() => {}, [])
const Child = React.memo(...)
```

---

## Vueとの違い

| Vue      | React             |
| -------- | ----------------- |
| v-model  | 手動                |
| computed | useMemo           |
| watch    | useEffect         |
| emit     | callback          |
| store    | Context / Zustand |

---

## アンチパターン

---

### useEffectでデータ生成

```jsx
useEffect(() => {
  setValue(...)
}, [])
```

---

### API直書き

```jsx
useEffect(() => {
  fetch(...)
}, [])
```

→ api.tsに分離

---

### state過多

```jsx
const [fullName, setFullName]
```

→ 不要

---

### 過剰useMemo / useCallback

---

## 実務での基本戦略

```txt
1. Serverでデータ取得
2. UIとロジックを分離
3. 必要なところだけClient化
4. 最適化は後から
```

---

## まとめ

* 実務はパターンの積み重ね
* 再利用可能な形で設計する
* 一貫性が重要
