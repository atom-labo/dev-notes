# Hooks

## 概要

Hooksは、関数コンポーネントで状態管理・副作用・最適化などを扱うための仕組み。

React学習では特に以下が重要。

- useState
- useEffect
- useMemo
- useCallback

---

## useState

コンポーネント内で状態を持つためのHook。

```jsx
const [count, setCount] = useState(0)
````

### ポイント

* `count` は現在の値
* `setCount` は値を更新する関数
* state更新後、コンポーネントは再レンダリングされる

```jsx
<button onClick={() => setCount(count + 1)}>
  {count}
</button>
```

---

## useStateの遅延初期化

初期値の計算が重い場合は、関数で渡す。

```jsx
const [value, setValue] = useState(() => {
  return heavyCalculation()
})
```

### 違い

```jsx
useState(heavyCalculation())      // 毎回評価される
useState(() => heavyCalculation()) // 初回だけ評価される
```

---

## useEffect

副作用を扱うためのHook。

副作用の例：

* API通信
* タイマー
* イベント登録
* localStorage
* document.title変更
* 外部ライブラリ連携

```jsx
useEffect(() => {
  // 副作用

  return () => {
    // cleanup
  }
}, [deps])
```

---

## 依存配列

### 初回のみ

```jsx
useEffect(() => {
  fetchData()
}, [])
```

Vueの `onMounted` に近い。

---

### 値が変わるたび

```jsx
useEffect(() => {
  console.log(count)
}, [count])
```

Vueの `watch` に近い。

---

### 毎回実行

```jsx
useEffect(() => {
  console.log('render')
})
```

基本的には多用しない。

---

## useEffectの注意点

### useEffectにasyncを直接渡さない

```jsx
useEffect(async () => {
  const data = await fetchData()
}, [])
```

これはNG。

```jsx
useEffect(() => {
  async function load() {
    const data = await fetchData()
  }

  load()
}, [])
```

---

### 無限ループに注意

```jsx
const [count, setCount] = useState(0)

useEffect(() => {
  setCount(count + 1)
}, [count])
```

流れ：

```txt
count変更
↓
useEffect実行
↓
setCount
↓
count変更
↓
useEffect再実行
```

---

### cleanupを書く

イベント登録やタイマーは解除する。

```jsx
useEffect(() => {
  const timerId = setInterval(() => {
    console.log('tick')
  }, 1000)

  return () => {
    clearInterval(timerId)
  }
}, [])
```

---

## useMemo

値の再計算を防ぐためのHook。

```jsx
const total = useMemo(() => {
  return items.reduce((sum, item) => sum + item.price, 0)
}, [items])
```

### 用途

* 重い計算のキャッシュ
* filter / sort / map の結果キャッシュ
* 子コンポーネントに渡す値の参照安定化

---

### 使うべきケース

```jsx
const filteredItems = useMemo(() => {
  return items.filter(item => item.active)
}, [items])
```

---

### 使わなくてよいケース

```jsx
const fullName = `${firstName} ${lastName}`
```

軽い計算には不要。

---

## useCallback

関数の参照を固定するためのHook。

```jsx
const handleClick = useCallback(() => {
  doSomething()
}, [])
```

### 用途

* memo化された子コンポーネントに関数を渡す
* useEffectの依存配列に関数を入れる
* 外部ライブラリへcallbackを渡す

---

## useMemoとuseCallbackの違い

| Hook        | 対象 | 目的         |
| ----------- | -- | ---------- |
| useMemo     | 値  | 値の再計算を防ぐ   |
| useCallback | 関数 | 関数の参照を固定する |

---

## useCallbackを使う例

```jsx
const Child = React.memo(({ onClick }) => {
  return <button onClick={onClick}>Click</button>
})

function Parent() {
  const handleClick = useCallback(() => {
    console.log('click')
  }, [])

  return <Child onClick={handleClick} />
}
```

`Child` に渡す関数の参照が安定するため、不要な再レンダリングを抑えられる。

---

## React.memoとの関係

* `useMemo`：値をキャッシュ
* `useCallback`：関数をキャッシュ
* `React.memo`：コンポーネントの再レンダリングを抑制

```jsx
const data = useMemo(() => compute(items), [items])

const handleClick = useCallback(() => {
  doSomething()
}, [])

const Child = React.memo(({ data, onClick }) => {
  return <button onClick={onClick}>{data}</button>
})
```

---

## 関数の渡し方

### 即実行

```jsx
fn()
```

今すぐ関数を実行する。

---

### 関数を渡す

```jsx
fn
```

Reactに関数そのものを渡す。

---

### 後で実行する関数を作る

```jsx
() => fn()
```

実行タイミングを遅らせる。

---

## イベントハンドラの例

```jsx
<button onClick={handleClick} />
```

または

```jsx
<button onClick={() => handleClick(id)} />
```

NG：

```jsx
<button onClick={handleClick()} />
```

これはレンダリング時に即実行される。

---

## Vue.jsとの違い

| Vue             | React              |
| --------------- | ------------------ |
| ref / reactive  | useState           |
| computed        | useMemo            |
| watch           | useEffect          |
| onMounted       | useEffect + `[]`   |
| onBeforeUnmount | useEffectのcleanup  |
| methods         | 通常関数 / useCallback |

---

## 実務での判断基準

### useEffect

使う：

* API通信
* 外部イベント
* タイマー
* localStorage
* DOM外の世界との同期

使わない：

* 単純な値の加工
* propsから値を作るだけ
* stateから派生値を作るだけ

---

### useMemo

使う：

* 重い計算
* 大量データのfilter / sort
* propsの参照を安定させたい

使わない：

* 単純な文字列結合
* 軽い計算

---

### useCallback

使う：

* memo化された子に関数を渡す
* useEffectの依存に関数が入る
* 外部ライブラリにcallbackを渡す

使わない：

* 単純なボタンイベント
* 再レンダリングが問題になっていない箇所

---

## アンチパターン

### useEffectで派生値を作る

```jsx
const [fullName, setFullName] = useState('')

useEffect(() => {
  setFullName(`${firstName} ${lastName}`)
}, [firstName, lastName])
```

これは不要。

```jsx
const fullName = `${firstName} ${lastName}`
```

で十分。

---

### なんでもuseMemo / useCallbackする

過剰最適化になる。

* 可読性が落ちる
* 依存配列ミスが増える
* 効果が薄いケースが多い

---

### 依存配列を空にしすぎる

```jsx
useEffect(() => {
  console.log(count)
}, [])
```

`count` が更新されてもEffectは再実行されない。
古い値を参照する原因になる。

---

## まとめ

* HooksはReactの中心
* useStateは状態管理
* useEffectは副作用
* useMemoは値のキャッシュ
* useCallbackは関数参照の固定
* 依存配列の理解が重要
* 最適化Hookは必要になってから使う
