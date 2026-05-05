# Object Types

オブジェクト型の定義と設計パターンを整理する。  
TypeScriptではオブジェクト構造＝設計そのもの。

---

## 概要

- オブジェクトの構造を型で定義する
- APIレスポンス / props / 状態管理の基盤
- 実務では最も使用頻度が高い

---

## 1. type と interface

### type

```ts
type User = {
  name: string;
  age: number;
};
````

---

### interface

```ts
interface User {
  name: string;
  age: number;
}
```

---

## 違い

| 観点    | type | interface |
| ----- | ---- | --------- |
| 拡張    | △    | ◎         |
| Union | ◎    | ×         |
| 用途    | 汎用   | オブジェクト    |

---

## 実務指針

* オブジェクト定義 → `interface`
* Union / 複雑型 → `type`

---

## 2. optional / readonly

### optional

```ts
type User = {
  name: string;
  age?: number;
};
```

---

### readonly

```ts
type User = {
  readonly id: number;
};
```

---

## 3. ネスト構造

```ts
type User = {
  name: string;
  address: {
    city: string;
    zip: string;
  };
};
```

---

## 4. インデックスシグネチャ

```ts
type Dictionary = {
  [key: string]: string;
};
```

---

## 5. Record（重要）

```ts
type UserMap = Record<string, User>;
```

↓

```ts
{
  [key: string]: User;
}
```

---

### 実務例

```ts
type StatusMap = Record<string, "success" | "error">;
```

---

## 6. 型の拡張

### interface extends

```ts
interface User {
  name: string;
}

interface Admin extends User {
  role: string;
}
```

---

### type &（Intersection）

```ts
type User = {
  name: string;
};

type Admin = User & {
  role: string;
};
```

---

## 7. 型の再利用

### Pick

```ts
type UserName = Pick<User, "name">;
```

---

### Omit

```ts
type UserWithoutAge = Omit<User, "age">;
```

---

### Partial

```ts
type PartialUser = Partial<User>;
```

---

## 8. 定数 + 型パターン（重要）

```ts
const STATUS = {
  SUCCESS: "success",
  ERROR: "error",
} as const;
```

---

### 型生成

```ts
type Status = typeof STATUS[keyof typeof STATUS];
```

↓

```ts
"success" | "error"
```

---

## ポイント

* 値と型を同期できる
* 手書きUnion不要

---

## 9. APIレスポンス設計

```ts
interface User {
  id: number;
  name: string;
}

interface ApiResponse {
  data: User;
}
```

---

## 実務ポイント

* フロント / バックのI/Fとして利用
* 型 = API契約

---

## 10. Reactでの利用

### props

```ts
type Props = {
  user: User;
};
```

---

### state

```ts
const [user, setUser] = useState<User | null>(null);
```

---

## 11. よくあるアンチパターン

### anyを使う

```ts
type User = any;
```

---

### 型を重複定義

```ts
type User = { name: string };
type UserDto = { name: string };
```

---

## まとめ

* オブジェクト型は設計の中心
* `type` と `interface` を使い分ける
* Utility Typesで再利用する
* 定数 + 型生成が重要

---

## 補足

オブジェクト型は以下と組み合わせる：

* 型操作（typeof / keyof）
* ジェネリクス
* API設計
