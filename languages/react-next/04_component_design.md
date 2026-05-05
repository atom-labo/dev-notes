# コンポーネント設計

## 概要

Reactにおける設計の中心は「責務分離」。  
UI・ロジック・データ取得を適切に分けることで、可読性・再利用性・保守性が向上する。

---

## 基本原則

### 1. 単一責務

- 1コンポーネント = 1責務
- 「何をするコンポーネントか」を明確にする

---

### 2. 小さく分割

```txt
Page
 ├ Header
 ├ List
 └ Item
````

* 大きいコンポーネントは分割する
* 再利用性を高める

---

### 3. 親 → 子 の一方向データフロー

```jsx
<Child data={data} onClick={handleClick} />
```

* 親がデータを持つ
* 子は表示・通知のみ

---

## コンポーネントの種類

---

### Container / Presentational

#### Container（ロジック）

```jsx
function UserListContainer() {
  const [users, setUsers] = useState([])

  useEffect(() => {
    fetchUsers().then(setUsers)
  }, [])

  return <UserList users={users} />
}
```

---

#### Presentational（表示）

```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

---

### ポイント

* ロジックとUIを分離
* テストしやすくなる
* 再利用しやすい

---

## カスタムフック

ロジックを再利用するための仕組み。

```jsx
function useUsers() {
  const [users, setUsers] = useState([])

  useEffect(() => {
    fetchUsers().then(setUsers)
  }, [])

  return users
}
```

```jsx
function UserList() {
  const users = useUsers()

  return (...)
}
```

---

### メリット

* ロジックの再利用
* コンポーネントがシンプルになる

---

## props設計

### シンプルに保つ

```jsx
<Child user={user} />
```

---

### 必要なものだけ渡す

```jsx
<Child name={user.name} />
```

---

### 避ける

```jsx
<Child data={hugeObject} />
```

👉 依存が増える

---

## childrenの活用

```jsx
function Layout({ children }) {
  return <div>{children}</div>
}
```

```jsx
<Layout>
  <Page />
</Layout>
```

---

### 用途

* レイアウト
* ラッパーコンポーネント

---

## 状態の配置

### 原則

> stateは「最も近い共通の親」に置く

---

### 例

```txt
Parent
 ├ ChildA
 └ ChildB
```

→ Parentにstateを持たせる

---

## 状態の持ちすぎに注意

```jsx
const [fullName, setFullName] = useState('')
```

→ 不要なstate

```jsx
const fullName = `${firstName} ${lastName}`
```

---

## コロケーション（近接配置）

関連するものは近くに置く。

```txt
User/
├ User.tsx
├ useUser.ts
├ User.test.ts
```

---

## ディレクトリ設計（例）

今回の現場構成に近い形：

```txt
features/pages/
└ user/
  ├ UserPage.tsx
  ├ useUser.ts
  ├ api.ts
  └ types.ts
```

---

## 再利用レベル

```txt
pages（画面）
↓
features（機能）
↓
shared（共通）
↓
parts（UI部品）
```

---

## コンポーネント分割の判断基準

分割する：

* 100行を超える
* 責務が複数ある
* 再利用できそう

---

分割しない：

* 単純なUI
* 再利用しない
* 過剰分割になる場合

---

## アンチパターン

---

### ロジックとUIが密結合

```jsx
function Component() {
  const [data, setData] = useState()

  useEffect(() => {
    fetch(...)
  }, [])

  return <div>{data}</div>
}
```

→ 分離可能

---

### propsドリル

```txt
A → B → C → D
```

すべてpropsで渡す

→ 可読性低下

---

対策：

* Context
* 状態管理ライブラリ

---

### 巨大コンポーネント

* 1ファイルにすべて詰め込む

---

## Vue.jsとの違い

| Vue                        | React      |
| -------------------------- | ---------- |
| SFC（template/script/style） | JSX中心      |
| Composition API            | カスタムフック    |
| props + emit               | props + 関数 |
| 自動分離されやすい                  | 意識的に分離が必要  |

---

## 実務での判断基準

* UIとロジックは分離する
* stateは最小限
* コンポーネントは小さく保つ
* カスタムフックでロジックをまとめる
* 再利用性を意識する

---

## まとめ

* コンポーネント設計はReactの品質を左右する
* 責務分離が最重要
* カスタムフックがキー
* 過剰設計にも注意
