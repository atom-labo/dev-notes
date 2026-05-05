# 用語集（Glossary）

## 概要

React / Next.jsで頻出する用語を整理する。  
暫定版のため、必要に応じて随時追加・更新する。

---

## 基本概念

### コンポーネント（Component）

UIを構成する最小単位。  
関数として定義され、stateやpropsを元にUIを返す。

---

### props

親コンポーネントから子へ渡されるデータ。

```jsx
<Child value={value} />
````

---

### state

コンポーネント内で管理する状態。

```jsx
const [count, setCount] = useState(0)
```

---

### 再レンダリング（Re-render）

stateやpropsの変更により、コンポーネント関数が再実行されること。

---

### 仮想DOM（Virtual DOM）

React内部で管理されるDOMの仮想表現。
差分比較により効率的に更新される。

---

## Hooks関連

### useState

状態を管理するHook。

---

### useEffect

副作用を扱うHook。

---

### useMemo

値の再計算を防ぐためのHook（メモ化）。

---

### useCallback

関数の参照を固定するためのHook。

---

### カスタムフック（Custom Hook）

ロジックを再利用するためのHook。

```jsx
function useUsers() { ... }
```

---

### 依存配列（Dependency Array）

useEffect / useMemo / useCallbackにおいて、再実行条件を指定する配列。

---

### stale closure

古いstateやpropsを参照してしまう問題。

---

## レンダリング関連

### CSR（Client Side Rendering）

ブラウザ側でJavaScriptにより描画する方式。

---

### SSR（Server Side Rendering）

サーバー側でHTMLを生成して返す方式。

---

### SSG（Static Site Generation）

ビルド時にHTMLを生成する方式。

---

### ISR（Incremental Static Regeneration）

一定時間ごとに再生成するSSGの拡張。

---

### Hydration

サーバーで生成されたHTMLに対して、クライアントでReactがイベント等を紐付ける処理。

---

### Hydrationエラー

サーバーとクライアントでHTMLが一致しないときに発生するエラー。

---

## Next.js関連

### pages Router

従来のNext.jsルーティング方式。

```txt
src/pages/
```

---

### App Router

新しいルーティング方式。

```txt
src/app/
```

---

### Server Component

サーバーで実行されるコンポーネント。
デフォルト。

---

### Client Component

ブラウザで実行されるコンポーネント。

```jsx
"use client"
```

---

### getServerSideProps

SSRを行うための関数（pages Router）。

---

### context（SSR）

リクエスト情報を含むオブジェクト。

```txt
req, res, query, params
```

---

### fetch（Next.js）

Next.js独自の拡張あり。

* デフォルトでキャッシュ
* revalidate指定可能

---

## パフォーマンス関連

### メモ化（Memoization）

計算結果をキャッシュして再利用すること。

---

### React.memo

コンポーネントの再レンダリングを防ぐための仕組み。

---

### 再レンダリング

コンポーネントが再実行されること。

---

### 同一性（Identity）

`===` で比較される値の一致性。
オブジェクトや関数は毎回新規生成されるため注意。

---

## 設計関連

### 単方向データフロー

親から子へデータが流れる設計。

---

### Container / Presentational

* Container：ロジック担当
* Presentational：UI担当

---

### カスタムフック

ロジックの再利用単位。

---

### propsドリル

深い階層までpropsを渡し続けること。

---

### Context

グローバルに値を共有する仕組み。

---

### コロケーション（Colocation）

関連するコードを近くに配置する設計。

---

## その他

### Controlled Component

入力値をstateで管理するコンポーネント。

---

### Uncontrolled Component

DOMが状態を持つコンポーネント。

---

### key

リストレンダリング時の識別子。

---

### 再生成（Re-create）

関数やオブジェクトが毎回新しく作られること。

---

## まとめ

* 用語理解はトラブルシュートに直結する
* 特に「再レンダリング」「依存配列」「Server/Client」は重要
* 実務で詰まった用語は随時追加する
