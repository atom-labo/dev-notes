# Type System

TypeScriptの型システムの基礎を整理する。  
JavaScriptとの違いの本質はここにある。

---

## 概要

TypeScriptは、JavaScriptに**静的型システム**を追加した言語。

- 実行前（コンパイル時）に型チェックが可能
- 型によってバグを未然に防ぐ
- 型は設計の一部として扱う

---

## 1. 基本型（Primitive Types）

```ts
let name: string = "Tomoki";
let age: number = 35;
let isActive: boolean = true;
````

---

### null / undefined

```ts
let value: string | null = null;
let data: string | undefined = undefined;
```

---

### ポイント

* `strict` モードでは明示的に扱う必要あり
* 実務では `null` を使うことが多い

---

## 2. Union型

```ts
let id: string | number;
```

### 意味

* 複数の型を許容する

---

### 実務例

```ts
type Status = "success" | "error";
```

---

## 3. Literal型

```ts
type Direction = "up" | "down";
```

### ポイント

* 値そのものを型として扱う
* 制約をかける用途で使う

---

## 4. any（非推奨）

```ts
let data: any;
```

### 問題

* 型チェックが無効になる
* TypeScriptのメリットが消える

---

## 5. unknown（推奨）

```ts
let data: unknown;
```

### 特徴

* 使用前に型チェックが必要
* 安全なany

---

### 例

```ts
if (typeof data === "string") {
  data.toUpperCase();
}
```

---

## 6. never

```ts
function error(): never {
  throw new Error("error");
}
```

### 意味

* 「絶対に値を返さない型」

---

### 用途

* エラー関数
* 到達不能コード

---

## 7. 型の絞り込み（Narrowing）

Union型を条件分岐で安全に絞る。

---

### typeof

```ts
function fn(value: string | number) {
  if (typeof value === "string") {
    value.toUpperCase();
  }
}
```

---

### in

```ts
type A = { a: string };
type B = { b: number };

function fn(x: A | B) {
  if ("a" in x) {
    x.a;
  }
}
```

---

## 8. 型ガード（Type Guard）

```ts
function isString(value: unknown): value is string {
  return typeof value === "string";
}
```

---

### 使用例

```ts
if (isString(data)) {
  data.toUpperCase();
}
```

---

### ポイント

* `value is 型` が特徴
* if内で型が確定する

---

## 9. nullチェック（重要）

```ts
type User = {
  name: string;
};

const user: User | null = null;

if (user) {
  user.name;
}
```

---

### ポイント

* 実務ではほぼ必須
* APIレスポンスでは特に重要

---

## 10. 型システムの本質

TypeScriptの型は：

* 実行時には存在しない
* コンパイル時の安全性を保証する

---

## 11. JavaScriptとの違い

| 観点    | JavaScript | TypeScript |
| ----- | ---------- | ---------- |
| 型     | 動的         | 静的         |
| エラー検出 | 実行時        | コンパイル時     |
| 安全性   | 低い         | 高い         |

---

## まとめ

* Union型で柔軟性を持たせる
* unknownで安全に扱う
* anyは避ける
* Narrowingで型を確定させる

---

## 補足

型システムは以下と組み合わせて使う：

* 型操作（typeof / keyof）
* ジェネリクス
* API設計

---

## 実務での重要度

高い順：

1. Union型
2. unknown
3. Narrowing
4. 型ガード
5. nullチェック
