# レンダリングとパフォーマンス

## 概要

Reactは「再レンダリング前提」で動作する。  
パフォーマンス最適化は重要だが、**前提理解を誤ると逆効果になる**。

---

## 再レンダリングとは

```jsx
function Component() {
  console.log("render")
  return <div />
}
````

再レンダリングが起きる条件：

* state変更
* props変更
* 親コンポーネントの再レンダリング

---

## 重要な前提

> コンポーネントは「再実行される」のが正常

---

## Vueとの違い

| Vue        | React    |
| ---------- | -------- |
| 部分的に更新     | 関数ごと再実行  |
| リアクティブ依存追跡 | 明示的な再実行  |
| 再描画を意識しにくい | 再実行前提で設計 |

---

## 再レンダリングの流れ

```txt
state変更
↓
コンポーネント関数再実行
↓
新しいUI生成（仮想DOM）
↓
差分だけ実DOMに反映
```

---

## 誤解

### ❌ 再レンダリング = 重い

→ 基本的には軽い

理由：

* 仮想DOMで差分更新
* 関数実行は軽量

---

## 問題になるケース

* 重い計算がある
* 大量リスト
* 不要な子コンポーネントまで再描画
* APIなど副作用が無駄に走る

---

## 再レンダリングの伝播

```jsx
function Parent() {
  return <Child />
}
```

Parentが再レンダリングされると：

→ Childも再レンダリングされる

---

## 不要な再レンダリングの原因

### 関数の再生成

```jsx
<Child onClick={() => doSomething()} />
```

毎回新しい関数になる

---

### オブジェクトの再生成

```jsx
<Child style={{ color: "red" }} />
```

毎回新しいオブジェクト

---

## 最適化手法

---

### React.memo

```jsx
const Child = React.memo(function Child(props) {
  return <div>{props.value}</div>
})
```

* propsが同じなら再レンダリングしない

---

### useMemo

```jsx
const value = useMemo(() => compute(data), [data])
```

* 値の再計算を防ぐ

---

### useCallback

```jsx
const handleClick = useCallback(() => {
  doSomething()
}, [])
```

* 関数の参照を固定する

---

## 3つの役割

| 対象      | 手法          |
| ------- | ----------- |
| 値       | useMemo     |
| 関数      | useCallback |
| コンポーネント | React.memo  |

---

## 組み合わせパターン

```jsx
const Child = React.memo(({ data, onClick }) => {
  return <button onClick={onClick}>{data}</button>
})

function Parent({ items }) {
  const data = useMemo(() => compute(items), [items])

  const handleClick = useCallback(() => {
    doSomething()
  }, [])

  return <Child data={data} onClick={handleClick} />
}
```

---

## 参照の重要性

Reactは基本的に `===` で比較する。

```jsx
[] === [] // false
{} === {} // false
```

👉 新しい配列・オブジェクトは別物として扱われる

---

## よくあるNG

### useMemoしないケース

```jsx
const filtered = items.filter(...)
```

→ 毎回新しい配列
→ Childが再レンダリング

---

### useMemoあり

```jsx
const filtered = useMemo(() => items.filter(...), [items])
```

→ 同じ参照を維持

---

## 最適化の判断基準

### 最適化する

* リストが重い（数百〜数千件）
* 明らかに再描画が遅い
* DevToolsで問題が確認できる

---

### 最適化しない

* 小規模コンポーネント
* 軽い計算
* 体感差がない

---

## アンチパターン

### 最初から最適化

```jsx
useMemo(...)
useCallback(...)
```

→ 可読性低下
→ バグ増加
→ 効果がないことが多い

---

### useEffectで制御

```jsx
useEffect(() => {
  setState(...)
}, [])
```

→ 設計ミスの可能性

---

## 設計の基本

* stateは最小限
* 派生値は計算で出す
* propsはシンプルに保つ
* UIは純粋関数として扱う

---

## React的な考え方

### NG

* 再レンダリングを避ける設計

---

### OK

* 再レンダリングされても問題ない設計

---

## デバッグ観点

* console.logでrender回数を見る
* 不要な再描画を特定する
* DevToolsで確認

---

## まとめ

* 再レンダリングはReactの基本動作
* 問題になるのは一部ケースのみ
* 最適化は必要になってから行う
* 参照（===）が重要な概念
