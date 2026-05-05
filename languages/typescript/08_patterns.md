# Patterns

TypeScriptの実務パターン集。  
現場で頻出する「定石」を整理する。

---

## 概要

- 実務で繰り返し使うパターンを集約
- 設計・保守性・型安全を高めるための指針
- 基礎（型システム / 型操作）を前提とする

---

## 1. 定数 + 型生成（最重要）

### パターン

```ts
const PATHS = {
  HOME: "/home",
  ABOUT: "/about",
} as const;

type Path = typeof PATHS[keyof typeof PATHS];
````

---

### 効果

* 値と型を同期
* 手書きUnionを排除

---

### 使用例

```ts
function navigate(path: Path) {}
```

---

## 2. エラーコード管理

### 定義

```ts
const ERROR_CODE = {
  INTERNAL_ERROR: "E-0001",
  INVALID: "E-0002",
} as const;

type ErrorCode =
  typeof ERROR_CODE[keyof typeof ERROR_CODE];
```

---

### 型ガード

```ts
function isErrorCode(code: unknown): code is ErrorCode {
  return (
    typeof code === "string" &&
    Object.values(ERROR_CODE).includes(code as ErrorCode)
  );
}
```

---

### ポイント

* 外部入力を安全に扱える
* APIレスポンスとの相性が良い

---

## 3. ルーティングパターン

### 型制約付きルーター

```ts
type Path = "/home" | "/about";

function navigate(path: Path) {}
```

---

### 定数 + 型

```ts
const PATHS = {
  HOME: "/home",
  ABOUT: "/about",
} as const;

type Path = typeof PATHS[keyof typeof PATHS];
```

---

## 4. API取得パターン

```ts
async function fetchData<T>(url: string): Promise<T> {
  const res = await fetch(url);
  return res.json();
}
```

---

### 使用

```ts
type User = {
  id: number;
};

const user = await fetchData<User>("/api/user");
```

---

### 注意

* 型は保証されない（バリデーション必要）

---

## 5. 型ガードパターン

```ts
function isString(value: unknown): value is string {
  return typeof value === "string";
}
```

---

### 応用

```ts
if (isString(data)) {
  data.toUpperCase();
}
```

---

## 6. null安全パターン

```ts
const [user, setUser] = useState<User | null>(null);

if (!user) return null;
```

---

### ポイント

* 初期状態はnull
* 条件分岐で型確定

---

## 7. フォームパターン

```ts
type UserForm = Partial<User>;
```

---

### 意味

* 入力途中の状態を表現

---

## 8. Utility Types活用

---

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

### Record

```ts
type UserMap = Record<string, User>;
```

---

## 9. 型再利用パターン

```ts
type ApiResponse<T> = {
  data: T;
};
```

---

### 使用例

```ts
type UserResponse = ApiResponse<User>;
```

---

## 10. React Hookパターン

---

### useState

```ts
const [data, setData] = useState<T | null>(null);
```

---

### useRef

```ts
const ref = useRef<HTMLInputElement>(null);
```

---

### カスタムHook

```ts
function useFetch<T>(url: string): T | null {}
```

---

## 11. アンチパターン

---

### anyの使用

```ts
const data: any = {};
```

---

### 型の重複

```ts
type A = { name: string };
type B = { name: string };
```

---

### 型と値の不一致

```ts
type Path = "/home";
const PATH = "/about";
```

---

## まとめ

* 定数 + 型生成が最重要
* 型ガードで安全性を担保
* Utility Typesで再利用
* null前提で設計

---

## 補足

パターンは随時追加していく：

* 実務で出た問題
* バグ対応
* 設計改善
