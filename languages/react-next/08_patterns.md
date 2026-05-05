# 実務パターン（Patterns）

## 概要

React / Next.jsの実務では、

```txt
毎回ゼロから考えず、再利用可能なパターンで構築する
````

ことが重要。

---

# 1. SSRページパターン

```txt
pages
↓
getServerSideProps
↓
データ取得
↓
props生成
↓
Component描画
```

---

## ポイント

```txt
Componentで直接APIを呼ばない
props駆動にする
```

---

# 2. Client初期化パターン

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
認証ページ
初期化処理
リダイレクト制御
```

---

# 3. API分離パターン

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
UIから直接HTTP通信しない
1API = 1関数
```

---

# 4. HTTPクライアント共通化

```txt
httpClient
httpClientWithAuth
```

---

## 用途

```txt
通常API / 認証付きAPIの分離
```

---

# 5. interceptorパターン

```txt
request → ヘッダー付与
response → エラー共通処理
```

---

# 6. Container / Presentational

```txt
Container（ロジック）
↓
Presentational（UI）
```

---

## 原則

```txt
UIはpropsのみで動く
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
ロジックの再利用
UIと分離
```

---

# 8. フォームパターン例

## 構成

```txt
FormComponent
↓
useForm
↓
schema（バリデーション）
↓
API送信
```

---

## 送信フロー

```txt
入力
↓
handleSubmit
↓
バリデーション
↓
API送信
↓
成功 / 失敗
```

---

## 送信中制御

```tsx
const [isSubmitting, setIsSubmitting] = useState(false)

<button disabled={isSubmitting}>送信</button>
```

---

## APIエラー表示

```tsx
setError("root", {
  message: "送信に失敗しました"
})
```

---

## Controller（外部UI）

```tsx
<Controller
  name="date"
  control={control}
  render={({ field }) => (
    <DatePicker {...field} />
  )}
/>
```

---

## 初期値 / リセット

```tsx
useForm({
  defaultValues: { name: "" }
})

reset()
```

---

# 9. ローディング制御

```txt
isLoading
isInitialized
```

---

## パターン

```tsx
if (isLoading && !isInitialized) return <Loading />
```

---

# 10. リダイレクト制御

```ts
router.push()
router.replace()
```

---

## パターン

```ts
if (!allowed) {
  router.replace("/404")
}
```

---

# 11. データ変換（Presenter）

```txt
APIレスポンス
↓
UI用データに変換
↓
Component
```

---

## 例

```ts
const items = data.map(x => ({
  label: x.name,
  value: x.id
}))
```

---

# 12. 非同期処理パターン

## 並列

```ts
await Promise.all([
  fetchA(),
  fetchB()
])
```

---

## 依存あり

```ts
const a = await fetchA()
await fetchB(a.id)
```

---

# 13. ディレクトリ構成例

```txt
features/
└ page/
  ├ Component.tsx
  ├ api.ts
  ├ hooks.ts
  ├ types.ts
```

---

# 14. 状態管理パターン

```txt
小規模 → useState
中規模 → Context
大規模 → 状態管理ライブラリ
```

---

# 15. アンチパターン

---

## ❌ UIにロジック詰め込み

```txt
Componentに全部書く
```

---

## ❌ 直接HTTP通信

```txt
useEffectで直接API
```

---

## ❌ 二重データ取得

```txt
Server + Client
```

---

## ❌ useEffect乱用

```txt
依存配列ミス
```

---

# まとめ

* 実務はパターンで組む
* データ取得とUIを分離する
* フォーム・非同期・認証は定型化できる
* 再利用性を意識する
