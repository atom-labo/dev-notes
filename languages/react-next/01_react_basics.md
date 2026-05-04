# React基礎

## 概要

Reactは「状態（state）に応じてUIを宣言的に構築する」ライブラリ。  
Vue.jsと比較すると、テンプレートではなく**JavaScript中心（JSX）でUIを記述する**点が大きな特徴。

---

## コア概念

### 宣言的UI（Declarative UI）

```jsx
const [count, setCount] = useState(0)

return <button>{count}</button>
````

* UIは「状態の結果」
* DOMを直接操作しない

---

### 再レンダリング前提の設計

* state / props が変わるとコンポーネント関数が再実行される
* 差分更新はReactが内部で処理（仮想DOM）

---

### 単方向データフロー

```txt
親 → 子（props）
```

* 子から親へは関数で通知

---

## JSX

```jsx
<div className="box">{message}</div>
```

特徴：

* HTMLではなくJavaScript
* `class` → `className`
* `{{ }}` → `{ }`
* 条件分岐・ループはJSで書く

---

## 条件分岐・ループ

```jsx
{isLoggedIn ? <A /> : <B />}

{items.map(item => (
  <Item key={item.id} />
))}
```

---

## コンポーネント

```jsx
function Component() {
  return <div>Hello</div>
}
```

* 関数ベースが主流
* UIは関数として定義される

---

## 状態（State）

```jsx
const [count, setCount] = useState(0)
```

* state変更 → 再レンダリング

---

## イベント

```jsx
<button onClick={handleClick} />
```

* 関数を渡す（即実行しない）

---

## Vue.jsとの違い

| 観点      | Vue            | React        |
| ------- | -------------- | ------------ |
| 記述      | テンプレート         | JSX（JS）      |
| 状態      | ref / reactive | useState     |
| 双方向バインド | v-model        | 手動           |
| 条件分岐    | v-if           | JS           |
| ループ     | v-for          | map          |
| イベント    | emit           | 関数渡し         |
| ライフサイクル | 分割API          | useEffectに集約 |

---

## 重要な意識

### Vue的思考

* どこが更新されるかを考える

### React的思考

* **再実行されても問題ない設計にする**

---

## 実務での判断基準

* stateは必要最小限
* 派生値は計算で求める（stateにしない）
* UIは関数として組み立てる
* 副作用とUIを分離する

---

## アンチパターン

### DOM直接操作

```js
document.getElementById(...)
```

→ Reactの思想と衝突

---

### 不要なstate

```jsx
const [fullName, setFullName] = useState('')
```

→ 計算で十分

---

### 再レンダリングを避けようとする

→ Reactの前提と逆

---

## まとめ

* Reactは「UIを関数として表現する」ライブラリ
* 再レンダリング前提で設計する
* JSXはJavaScriptでUIを書く仕組み
* Vueよりも明示的で制御寄り
