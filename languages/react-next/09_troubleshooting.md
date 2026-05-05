# トラブルシュート

## 概要

React / Next.jsでよく発生する問題を、

```txt
原因
→ なぜ起きるか
→ 対処
````

の観点で整理する。

---

# 1. 無限レンダリング

## 症状

* 画面が固まる
* consoleが大量出力

---

## 原因

```tsx
useEffect(() => {
  setState(...)
}, [state])
```

---

## なぜ

```txt
state更新 → useEffect → setState → ループ
```

---

## 対処

* 依存配列を見直す
* 更新条件を制御する

---

# 2. useEffectが意図通り動かない

---

## ケース①：更新されない

```tsx
useEffect(() => {
  console.log(count)
}, [])
```

### 原因

```txt
依存配列にcountがない
```

---

## ケース②：毎回実行される

```tsx
useEffect(() => {
  fetchData()
}, [fn])
```

### 原因

```txt
fnが毎回新しく生成される
```

---

## 対処

```tsx
const fn = useCallback(() => {}, [])
```

---

# 3. stale closure

## 症状

* 古いstateを参照する

---

## 原因

```tsx
useEffect(() => {
  console.log(count)
}, [])
```

---

## 対処

* 依存配列を正しく書く

---

# 4. Hydrationエラー（Next.js）

## 症状

```txt
Text content does not match server-rendered HTML
```

---

## 原因

```tsx
const value = Math.random()
```

---

## 対処

```tsx
useEffect(() => {
  setValue(Math.random())
}, [])
```

---

# 5. key問題

## 症状

* リストが崩れる

---

## 原因

```tsx
key={index}
```

---

## 対処

```tsx
key={item.id}
```

---

# 6. イベント即実行

## NG

```tsx
<button onClick={handleClick()} />
```

---

## 対処

```tsx
<button onClick={handleClick} />
```

---

# 7. 再レンダリング過多

## 原因

* 毎回新しいオブジェクト
* 毎回新しい関数

---

## 対処

```tsx
useMemo
useCallback
React.memo
```

---

# 8. データ二重取得

## 原因

```txt
SSR + Client両方で取得
```

---

## 対処

```txt
どちらかに統一
```

---

# 9. useEffectでasync

## NG

```tsx
useEffect(async () => {})
```

---

## 対処

```tsx
useEffect(() => {
  async function load() {}
  load()
}, [])
```

---

# 10. Server / Client混同

## 症状

* useState使えない
* イベント動かない

---

## 原因

```txt
Server Componentで書いている
```

---

## 対処

```tsx
"use client"
```

---

# 11. router.queryがundefined

## 原因

```txt
初回レンダリングでは未確定
```

---

## 対処

```tsx
if (!router.isReady) return
```

---

# 12. エラーハンドリング設計

## 基本方針

```txt
エラーはレイヤーごとに責務分離
```

---

## エラー分類

```txt
401 → 未認証
403 → 権限なし
404 → 存在しない
500 → サーバーエラー
```

---

## レイヤー責務

```txt
axios（interceptor）
→ 共通処理

api.ts
→ throwするだけ

hooks
→ 状態管理

Component
→ UI表示
```

---

## 実務パターン

```txt
401 → interceptorでログインへ
404 → ページ単位でリダイレクト
バリデーション → フォーム表示
500 → toast or fallback UI
```

---

## NGパターン

```txt
全部catchで404
APIでエラー握りつぶす
401をUIで処理
```

---

# 13. 非同期処理ミス

## ❌ 無駄な直列

```tsx
await fetchA()
await fetchB()
```

---

## ❌ Promise未await

```tsx
fetchData()
```

---

## 対処

```tsx
await Promise.all([...])
```

---

# 14. 認証系の落とし穴

## ❌ SSRで認証API

```txt
localStorage使えない
```

---

## 対処

```txt
Client側で初期化
```

---

## ❌ 認証チェックしない

```tsx
await getUserProfile()
```

---

# 15. UX問題

## ❌ ローディングなし

```txt
画面が一瞬空白
```

---

## ❌ エラー表示なし

```txt
ユーザーに何も伝わらない
```

---

## 対処

```tsx
Loading / Error UIを出す
```

---

# Vueとの違い（トラブル観点）

| Vue        | React         |
| ---------- | ------------- |
| 自動依存追跡     | 手動依存配列        |
| watch安定    | useEffectミス多い |
| computed安全 | useMemoミスあり   |

---

# まとめ

* 多くの問題は「依存関係」と「再レンダリング」
* エラーはレイヤーごとに分離
* router.isReadyは重要
* 非同期処理ミスに注意
* SSRとClientの違いを理解する
