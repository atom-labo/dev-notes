# データ取得（Data Fetching）

## 概要

React / Next.jsにおけるデータ取得は、

```txt
どこで取得するか（Server / Client）
どのレイヤーで責務を持つか
````

が最重要。

---

# 全体整理

```txt
Serverで取得
├ SSR（getServerSideProps）
└ SSG / ISR

Clientで取得
├ useEffect
└ カスタムフック
```

---

# 1. SSR（Server Side Rendering）

```txt
request
↓
getServerSideProps
↓
データ取得関数
↓
props生成
↓
Component描画
```

---

## 例

```ts
export const getServerSideProps = async () => {
  const data = await fetchData()

  return {
    props: { data }
  }
}
```

---

## 特徴

```txt
- 初期HTMLにデータが含まれる
- SEOに強い
- 常に最新データ
```

---

## 向いているケース

```txt
- 公開ページ
- 初期表示に必須のデータ
- SEO重要ページ
```

---

# 2. Client側取得

```txt
Component
↓
useEffect / custom hook
↓
データ取得
↓
state更新
↓
描画
```

---

## 例

```ts
useEffect(() => {
  fetchData()
}, [])
```

---

## 特徴

```txt
- 初期HTMLにデータなし
- ローディングが必要
- SEOに弱い
```

---

## 向いているケース

```txt
- 認証が必要なページ
- ユーザー操作後の取得
- ブラウザ依存（localStorageなど）
```

---

# 3. SSRとClientの使い分け

```txt
公開ページ → Server
認証ページ → Client
```

---

## 理由

```txt
認証情報はClient側に依存するケースが多い
```

---

# 4. API層の設計

```txt
Component
↓
hooks
↓
api関数
↓
HTTPクライアント
```

---

## ルール

```txt
- UIから直接HTTP通信しない
- 1API = 1関数
- 型を明確にする
```

---

## 例

```ts
export const getListData = async () => {
  return httpClient.get("/items")
}
```

---

# 5. HTTPクライアントの共通化

```txt
共通HTTPクライアント
```

---

## 例

```ts
export const httpClient = createClient({
  baseURL: "...",
  timeout: 10000
})
```

---

# 6. 認証付き通信

```txt
認証付きクライアント
```

---

## 仕組み

```txt
request interceptor
→ 認証情報付与

response interceptor
→ エラー共通処理
```

---

# 7. カスタムフックパターン

```ts
export function useData() {
  const [data, setData] = useState(null)

  useEffect(() => {
    fetchData().then(setData)
  }, [])

  return data
}
```

---

## メリット

```txt
- 再利用可能
- UIとロジック分離
```

---

# 8. Client初期化パターン

```txt
Component
↓
初期化Hook
↓
認証チェック
↓
データ取得
↓
state更新
↓
描画
```

---

## 用途

```txt
- 認証チェック
- 初期データ取得
- リダイレクト制御
```

---

# 9. 非同期処理

## 直列（依存あり）

```ts
const a = await fetchA()
const b = await fetchB(a.id)
```

---

## 並列（依存なし）

```ts
const [a, b] = await Promise.all([
  fetchA(),
  fetchB()
])
```

---

## 注意点

```txt
- Promise.allはどれか1つ失敗で全体失敗
```

---

## 個別処理

```ts
const results = await Promise.allSettled([...])
```

---

# 10. エラーハンドリング

```ts
try {
  await fetchData()
} catch (e) {
  // 処理
}
```

---

## 実務分類

```txt
401 → 未認証
403 → 権限なし
404 → 存在しない
500 → サーバーエラー
```

---

# 11. アンチパターン

---

## ❌ UIで直接通信

```ts
useEffect(() => {
  axios.get(...)
}, [])
```

---

## ❌ 二重取得

```txt
Server取得 + Client取得
```

---

## ❌ 非同期ミス

```ts
fetchData() // awaitなし
```

---

# まとめ

* データ取得は「場所」と「責務」で考える
* SSRとClientを使い分ける
* API層を分離する
* 非同期処理は直列/並列を意識する
* エラーは適切に伝播させる
