# React / Next.js Types

React（Next.js）でのTypeScript利用を整理する。  
実務で頻出する型パターンを中心に扱う。

---

## 概要

- Reactでは「props」「state」「イベント」「DOM操作」に型が必要
- Next.jsでは「Server / Client」の違いも考慮する
- 型はUI設計・API設計と密接に関係する

---

## 1. コンポーネントの型

### 基本

```ts
type Props = {
  title: string;
};

export default function Component({ title }: Props) {
  return <div>{title}</div>;
}
````

---

### ポイント

* propsは必ず型定義する
* 関数コンポーネントで十分（`React.FC`は基本使わない）

---

## 2. children

```ts
type Props = {
  children: React.ReactNode;
};
```

---

### 意味

* JSXで渡される要素の型

---

### 使用例

```tsx
function Layout({ children }: Props) {
  return <div>{children}</div>;
}
```

---

## 3. useState

### 基本

```ts
const [count, setCount] = useState(0);
```

---

### 明示するケース

```ts
type User = {
  id: number;
  name: string;
};

const [user, setUser] = useState<User | null>(null);
```

---

### ポイント

* 非同期データは `null` を含める
* 初期値で型推論される

---

## 4. useRef

### DOM操作

```ts
const inputRef = useRef<HTMLInputElement>(null);
```

---

### 使用例

```ts
inputRef.current?.focus();
```

---

### ポイント

* 初期値は `null`
* `current` にDOMが入る

---

## 5. イベント型

### click

```ts
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {}
```

---

### change

```ts
function handleChange(e: React.ChangeEvent<HTMLInputElement>) {}
```

---

### ポイント

* DOM要素型をジェネリクスで指定
* IDE補完で確認するのが効率的

---

## 6. useEffect

```ts
useEffect(() => {
  // side effect
}, []);
```

---

### 非同期処理

```ts
useEffect(() => {
  const fetchData = async () => {
    // 処理
  };
  fetchData();
}, []);
```

---

## 7. カスタムHook

### 基本

```ts
function useFetch<T>(url: string): T | null {
  // ...
}
```

---

### 使用例

```ts
type User = {
  id: number;
  name: string;
};

const user = useFetch<User>("/api/user");
```

---

## 8. Next.js（App Router）

---

### Server Component

```ts
export default async function Page() {
  const res = await fetch("...");
  const data = await res.json();
  return <div>{data.name}</div>;
}
```

---

### ポイント

* デフォルトでServer Component
* 型は明示する方が安全

---

## 9. Client Component

```ts
"use client";

import { useState } from "react";
```

---

### ポイント

* `useState` / `useEffect` 使用時は必須
* `window` / `document` もここでのみ利用

---

## 10. 型安全なAPI連携

---

### 問題

```ts
const user: User = await res.json();
```

→ 実際のデータが違っても通る

---

### 解決（型ガード or スキーマ）

```ts
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null;
}
```

---

## 11. よくあるパターン

---

### props + API

```ts
type Props = {
  user: User;
};
```

---

### ローディング

```ts
const [user, setUser] = useState<User | null>(null);
```

---

### 条件レンダリング

```ts
if (!user) return null;
```

---

## 12. よくあるミス

---

### nullチェック忘れ

```ts
user.name; // エラー
```

---

### anyの使用

```ts
const data: any = await res.json();
```

---

### 型未定義

```ts
function Component(props) {}
```

---

## まとめ

* props / state / event を型で管理
* nullを前提に設計
* DOM操作はuseRef
* APIは型だけでなく検証も必要

---

## 補足

ReactでのTypeScriptは以下と密接：

* API設計
* 状態管理
* バリデーション
