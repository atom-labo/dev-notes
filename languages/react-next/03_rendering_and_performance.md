# レンダリングとパフォーマンス

## 概要

Reactのパフォーマンスは主に以下で決まる。

```txt
再レンダリング
計算コスト
DOM描画コスト
通信（API）
````

---

# 1. 基本理解

## Reactのレンダリング

```txt
state / props変更
↓
Component再実行
↓
差分計算（Virtual DOM）
↓
DOM更新
```

---

## ポイント

```txt
関数コンポーネントは「再実行」される
```

---

# 2. 再レンダリングの仕組み

## 親が更新されると

```txt
Parent再レンダリング
↓
Childも再レンダリング
```

---

## 問題になるケース

```txt
不要な再レンダリング
```

---

# 3. よくある原因

---

## ① 毎回新しいオブジェクト

```tsx
<Child options={{ sort: "asc" }} />
```

---

## ② 毎回新しい関数

```tsx
<Child onClick={() => doSomething()} />
```

---

## ③ 重い計算

```tsx
const result = heavyCalculation(data)
```

---

# 4. 最適化手段

---

## useMemo（値のキャッシュ）

```tsx
const value = useMemo(() => {
  return compute(data)
}, [data])
```

---

## useCallback（関数のキャッシュ）

```tsx
const handleClick = useCallback(() => {
  doSomething()
}, [])
```

---

## React.memo（コンポーネントのメモ化）

```tsx
const Child = React.memo(function Child(props) {
  return <div>{props.label}</div>
})
```

---

# 5. 使い分け

## useMemo

```txt
重い計算結果をキャッシュ
```

---

## useCallback

```txt
子コンポーネントに渡す関数を安定させる
```

---

## React.memo

```txt
propsが変わらない限り再レンダリングしない
```

---

# 6. 判断基準

## 最適化する

```txt
- 大量リスト
- 重い計算
- 体感で遅い
- memo化された子にpropsを渡す
```

---

## 最適化しない

```txt
- 単純な画面
- 軽い処理
- 問題が発生していない
```

---

# 7. 計測方法

## React DevTools（Profiler）

確認できる内容：

```txt
どのコンポーネントが
何回
どれくらい時間をかけて
再レンダリングされたか
```

---

## Chrome DevTools

### Performanceタブ

```txt
CPU / Rendering / Paint
```

---

### Networkタブ

```txt
APIの遅延
リクエスト回数
```

---

# 8. APIと描画は分けて考える

```txt
画面が遅い原因
↓
・レンダリングか？
・通信か？
```

---

# 9. 実務で多い改善

```txt
- 不要な再レンダリング削減
- useEffectの依存見直し
- APIの二重取得削減
- 重い処理のメモ化
```

---

# 10. アンチパターン

---

## ❌ 全部useMemo

```txt
逆に遅くなる
```

---

## ❌ 全部useCallback

```txt
可読性低下
```

---

## ❌ React.memo乱用

```txt
効果がないケースも多い
```

---

## ❌ 原因を測らない

```txt
勘で最適化
```

---

# 11. 再レンダリング最適化の流れ

```txt
① 遅い箇所を特定
② 原因分析（再レンダリング or API）
③ 必要な箇所だけ最適化
```

---

# 12. useEffectとの関係

```txt
useEffectの依存配列ミス
→ 無駄な再レンダリング
```

---

# 13. データ変換との関係

```txt
毎回map / filter
↓
無駄な計算
```

---

## 対策

```tsx
const items = useMemo(() => {
  return transform(data)
}, [data])
```

---

# 14. Vueとの対応

| Vue      | React       |
| -------- | ----------- |
| computed | useMemo     |
| methods  | useCallback |
| watch    | useEffect   |

---

# まとめ

* 再レンダリングが基本コスト
* 最適化は必要な箇所のみ
* DevToolsで計測する
* API遅延と描画遅延は分ける
* useMemo / useCallbackは適切に使う
