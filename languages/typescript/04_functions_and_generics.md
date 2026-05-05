# Functions and Generics

関数の型とジェネリクス（Generics）を整理する。  
TypeScriptにおける再利用性・拡張性の中核。

---

## 概要

- 関数は「引数」と「戻り値」に型を持つ
- ジェネリクスは「型を抽象化して再利用」する仕組み

---

## 1. 関数の型

### 基本

```ts
function add(a: number, b: number): number {
  return a + b;
}
````

---

### アロー関数

```ts
const add = (a: number, b: number): number => {
  return a + b;
};
```

---

### 戻り値の型

```ts
function log(message: string): void {
  console.log(message);
}
```

---

### 推論

```ts
function add(a: number, b: number) {
  return a + b; // numberと推論される
}
```

---

## 2. 関数型（型として定義）

```ts
type Add = (a: number, b: number) => number;
```

---

### 使用例

```ts
const add: Add = (a, b) => a + b;
```

---

## 3. Optional / Default引数

```ts
function greet(name?: string) {
  return `Hello ${name ?? "Guest"}`;
}
```

---

```ts
function greet(name: string = "Guest") {
  return `Hello ${name}`;
}
```

---

## 4. Generics（ジェネリクス）

### 基本

```ts
function identity<T>(arg: T): T {
  return arg;
}
```

---

### 意味

* 型を引数として受け取る
* 入力と出力の型を一致させる

---

### 使用例

```ts
identity<string>("test");
identity<number>(123);
```

---

## 5. 型推論との関係

```ts
identity("test"); // T = string と推論される
```

---

### ポイント

* 基本は明示しなくてよい
* 複雑な場合のみ指定

---

## 6. ジェネリクスの制約（extends）

```ts
function length<T extends { length: number }>(arg: T) {
  return arg.length;
}
```

---

### 意味

* `length` プロパティを持つ型に限定

---

### 使用例

```ts
length("abc");     // OK
length([1, 2, 3]); // OK
length(123);       // NG
```

---

## 7. ジェネリクスの命名

### よくある命名

| 名前            | 意味   |
| ------------- | ---- |
| T             | 汎用   |
| TData         | データ  |
| TPath         | パス   |
| TKey / TValue | Map系 |

---

### 実務例

```ts
function useFetch<TData>() {}
```

```ts
function useRouter<TPath extends string>() {}
```

---

### ポイント

* 小さいスコープ → `T`
* 意味が必要 → `T + 名詞`

---

## 8. 複数ジェネリクス

```ts
function map<T, U>(value: T, fn: (v: T) => U): U {
  return fn(value);
}
```

---

### 使用例

```ts
map(1, (v) => v.toString()); // U = string
```

---

## 9. Reactでのジェネリクス

---

### useState

```ts
const [user, setUser] = useState<User | null>(null);
```

---

### カスタムHook

```ts
function useFetch<T>(url: string): T | null {
  // ...
}
```

---

### コンポーネント

```ts
type Props<T> = {
  data: T;
};

function List<T>({ data }: Props<T>) {}
```

---

## 10. よくあるパターン

---

### API取得

```ts
async function fetchData<T>(url: string): Promise<T> {
  const res = await fetch(url);
  return res.json();
}
```

---

### 使用例

```ts
type User = {
  id: number;
  name: string;
};

const user = await fetchData<User>("/api/user");
```

---

## 11. ジェネリクスを使うべき場面

* 型を再利用したい
* 入力と出力が関連している
* コンポーネントやHookを汎用化したい

---

## 12. 使わない方がいい場面

* 型が固定されている
* 可読性が落ちる場合

---

## まとめ

* 関数は引数と戻り値に型を持つ
* ジェネリクスは型の抽象化
* `extends` で制約をかける
* 型推論が基本

---

## 補足

ジェネリクスは以下と組み合わせて使う：

* 型操作（typeof / keyof）
* 型ガード
* API設計
