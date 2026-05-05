# トラブルシュート

## 概要

React / Next.jsでよく遭遇する問題と、その原因・対処を整理する。  
まずは「典型パターン」を押さえることが重要。

---

## 1. 無限レンダリング

### 症状

- 画面が固まる
- CPU使用率が上がる
- コンソールが大量出力

---

### 原因

```jsx
useEffect(() => {
  setState(...)
}, [state])
````

---

### なぜ起きるか

```txt
state変更
↓
useEffect発火
↓
setState
↓
state変更
↓
ループ
```

---

### 対処

* 依存配列を見直す
* state更新条件を制御する
* useEffectの用途を見直す

---

## 2. useEffectが意図通り動かない

---

### ケース①：更新されない

```jsx
useEffect(() => {
  console.log(count)
}, [])
```

---

### 原因

* 依存配列に `count` がない

---

### 対処

```jsx
useEffect(() => {
  console.log(count)
}, [count])
```

---

### ケース②：毎回実行される

```jsx
useEffect(() => {
  fetchData()
}, [fn])
```

---

### 原因

* `fn` が毎回新しく生成されている

---

### 対処

```jsx
const fn = useCallback(() => {}, [])
```

---

## 3. stale closure（古い値を参照）

---

### 症状

* stateが更新されているのに古い値が使われる

---

### 例

```jsx
useEffect(() => {
  console.log(count)
}, [])
```

---

### 原因

* 初回レンダリング時の値を保持している

---

### 対処

* 依存配列を正しく書く
* useRefや関数型更新を検討

---

## 4. Hydrationエラー（Next.js）

---

### 症状

* Warning: Text content does not match server-rendered HTML

---

### 原因

* ServerとClientでHTMLが異なる

---

### 例

```jsx
const value = Math.random()
```

---

### 対処

* クライアント側でのみ実行

```jsx
"use client"
```

または

```jsx
useEffect(() => {
  setValue(Math.random())
}, [])
```

---

## 5. keyの問題

---

### 症状

* リストの表示が崩れる
* 状態が意図しない位置に残る

---

### 原因

```jsx
items.map((item, index) => (
  <Item key={index} />
))
```

---

### 対処

```jsx
<Item key={item.id} />
```

---

## 6. イベントが即実行される

---

### NG

```jsx
<button onClick={handleClick()} />
```

---

### 原因

* render時に関数が実行されている

---

### 対処

```jsx
<button onClick={handleClick} />
```

または

```jsx
<button onClick={() => handleClick(id)} />
```

---

## 7. 再レンダリングが多すぎる

---

### 原因

* 関数の再生成
* オブジェクトの再生成

---

### 対処

```jsx
useCallback(...)
useMemo(...)
React.memo(...)
```

※必要な場合のみ

---

## 8. データが二重取得される

---

### 原因

```txt
Serverで取得
↓
Clientでも取得
```

---

### 対処

* どちらかに統一
* 初期データをpropsで渡す

---

## 9. useEffectでasync直接使用

---

### NG

```jsx
useEffect(async () => {
  await fetch(...)
}, [])
```

---

### 対処

```jsx
useEffect(() => {
  async function load() {
    await fetch(...)
  }
  load()
}, [])
```

---

## 10. propsドリル

---

### 症状

```txt
A → B → C → D
```

---

### 対処

* Contextを使う
* 状態管理ライブラリを使う

---

## 11. Server / Clientの混同（Next.js）

---

### 症状

* useStateが使えない
* イベントが動かない

---

### 原因

* Server Componentで書いている

---

### 対処

```jsx
"use client"
```

---

## 12. fetchのキャッシュ問題

---

### 症状

* データが更新されない

---

### 原因

```jsx
fetch(url)
```

→ デフォルトキャッシュ

---

### 対処

```jsx
fetch(url, { cache: "no-store" })
```

または

```jsx
fetch(url, { next: { revalidate: 60 } })
```

---

## Vueとの違い（トラブル観点）

| Vue        | React        |
| ---------- | ------------ |
| 自動依存追跡     | 手動（依存配列）     |
| computed安全 | useMemoミスあり  |
| watch安定    | useEffect罠あり |
| 再描画制御簡単    | 再レンダリング前提    |

---

## 実務でのチェックポイント

* useEffectの依存配列
* stateの持ちすぎ
* 不要な再レンダリング
* Server / Clientの切り分け
* API取得場所

---

## まとめ

* 多くの問題は「依存関係」と「再レンダリング」に起因
* useEffectがトラブルの中心になりやすい
* Server / Clientの理解も重要
* まずは典型パターンを覚える
