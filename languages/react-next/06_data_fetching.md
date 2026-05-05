# データ取得（Data Fetching）

## 概要

React / Next.jsにおけるデータ取得は、

```txt
どこで取得するか（Server / Client）
````

が最重要。

---

## 全体整理

```txt
Serverで取得
├ getServerSideProps（pages Router）
└ Server Component（App Router）

Clientで取得
├ useEffect
└ カスタムフック
```

---

## 1. SSR（Server Side Rendering）

```txt
request
↓
getServerSideProps
↓
API取得
↓
props生成
↓
Component描画
```

---

### 例

```ts
export const getServerSideProps = async () => {
  const data = await fetchData()

  return {
    props: { data }
  }
}
```

---

### 特徴

* 初期HTMLにデータが含まれる
* SEOに強い
* 常に最新データ

---

### 向いているケース

```txt
- 公開ページ
- SEO重要
- 初期表示に必須のデータ
```

---

## 2. Client側取得

```txt
Component
↓
useEffect / custom hook
↓
API取得
↓
state更新
↓
描画
```

---

### 例

```ts
useEffect(() => {
  fetchData()
}, [])
```

---

### 特徴

* 初期HTMLにデータなし
* ローディングが必要
* SEOに弱い

---

### 向いているケース

```txt
- 認証ページ
- ユーザー操作後の取得
- localStorage依存
```

---

## 3. SSRとClientの使い分け（重要）

```txt
公開ページ
→ SSR

認証ページ
→ Client
```

---

### 理由

```txt
認証情報（token / session）
→ Client側でしか取得できない場合がある
```

---

## 4. API層の設計

```txt
Component
↓
hooks
↓
api.ts
↓
httpClient
```

---

### api.ts

```ts
export const getUsers = async () => {
  return httpClient.get("/users")
}
```

---

### ルール

* 画面からaxiosを直接呼ばない
* 1API = 1関数
* 型を定義する

---

## 5. axiosの共通化

```txt
libs/Axios.ts
```

---

### 例

```ts
export const httpClient = axios.create({
  baseURL: "...",
  timeout: 10000,
})
```

---

## 6. 認証付きAPI

```txt
httpClientWithCredentials
```

---

### 仕組み

```txt
request interceptor
→ 認証ヘッダー付与

response interceptor
→ 401時にリダイレクト
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

### メリット

* 再利用可能
* UIとロジック分離

---

## 8. Client初期化パターン（重要）

```txt
Component
↓
usePageInitialization
↓
API取得
↓
state更新
↓
描画
```

---

### 用途

```txt
- 認証チェック
- 初期データ取得
- リダイレクト制御
```

---

## 9. エラーハンドリング

```ts
try {
  await fetchData()
} catch (e) {
  // エラー処理
}
```

---

### 実務で必要な分類

```txt
- 401（未認証）
- 403（権限なし）
- 404（存在しない）
- 500（サーバーエラー）
```

---

## 10. アンチパターン

---

### UIで直接API呼び出し

```ts
useEffect(() => {
  axios.get(...)
}, [])
```

---

### ServerとClientで二重取得

```txt
SSR
↓
Clientでも再取得
```

---

### useEffect依存ミス

```ts
useEffect(() => {
  fetchData()
}, [])
```

---

## Vueとの対応

| Vue         | React              |
| ----------- | ------------------ |
| asyncData   | getServerSideProps |
| onMounted   | useEffect          |
| composables | custom hooks       |

---

## 実務での基本戦略

```txt
1. まずSSRを検討
2. 無理ならClient
3. APIは分離
4. hooksでラップ
```

---

## まとめ

* データ取得は「どこでやるか」が最重要
* SSRとClientを使い分ける
* API層を分離する
* 認証はClient側で扱うケースが多い
