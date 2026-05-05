# Type Operations

TypeScriptにおける「型操作」を整理する。  
実務で最も重要な領域の1つ。

---

## 概要

型操作とは：

- 値から型を生成する
- 型を抽出・変換する
- 型と値を同期する

---

## 1. typeof（値 → 型）

### 基本

```ts
const user = {
  name: "Tomoki",
  age: 35,
};

type User = typeof user;
````

↓

```ts
type User = {
  name: string;
  age: number;
};
```

---

### ポイント

* 値から型を生成できる
* 型の二重管理を防ぐ

---

## 2. keyof（キー取得）

```ts
type User = {
  name: string;
  age: number;
};

type Keys = keyof User;
```

↓

```ts
"name" | "age"
```

---

### ポイント

* オブジェクトのキーをUnion型で取得
* 動的アクセスと相性が良い

---

## 3. インデックスアクセス型

```ts
type User = {
  name: string;
  age: number;
};

type Name = User["name"];
```

↓

```ts
string
```

---

### Unionでの利用

```ts
type Value = User["name" | "age"];
```

↓

```ts
string | number
```

---

## 4. as const（リテラル型化）

```ts
const PATHS = {
  HOME: "/home",
  ABOUT: "/about",
} as const;
```

↓

```ts
{
  readonly HOME: "/home";
  readonly ABOUT: "/about";
}
```

---

### ポイント

* 値がリテラル型になる
* readonlyになる
* 定数管理に必須

---

## 5. typeof + keyof（キー取得）

```ts
type Keys = keyof typeof PATHS;
```

↓

```ts
"HOME" | "ABOUT"
```

---

## 6. 最重要パターン：値のUnion型生成

```ts
type Path = typeof PATHS[keyof typeof PATHS];
```

↓

```ts
"/home" | "/about"
```

---

### 分解

1. `typeof PATHS`
   → `{ HOME: "/home", ABOUT: "/about" }`

2. `keyof`
   → `"HOME" | "ABOUT"`

3. インデックスアクセス
   → `PATHS["HOME" | "ABOUT"]`

4. 結果
   → `"/home" | "/about"`

---

### 一言で

> オブジェクトの値一覧を型として取り出す

---

## 7. 実務パターン

---

### ① ルーティング

```ts
const PATHS = {
  HOME: "/home",
  ABOUT: "/about",
} as const;

type Path = typeof PATHS[keyof typeof PATHS];
```

---

### ② エラーコード

```ts
const ERROR_CODE = {
  INTERNAL_ERROR: "E-0001",
  INVALID: "E-0002",
} as const;

type ErrorCode =
  typeof ERROR_CODE[keyof typeof ERROR_CODE];
```

---

### ③ 関数制約

```ts
function moveTo(path: Path) {}

moveTo("/home");   // OK
moveTo("/invalid"); // NG
```

---

## 8. なぜ使うのか

---

### ❌ 手書き

```ts
type Path = "/home" | "/about";
```

---

### 問題

* 定数とズレる
* 修正漏れが発生

---

### ✅ 型自動生成

```ts
type Path = typeof PATHS[keyof typeof PATHS];
```

---

### メリット

* 値と型が完全同期
* 保守性が高い

---

## 9. enumとの比較

| 観点  | enum | const + 型 |
| --- | ---- | --------- |
| 柔軟性 | 低い   | 高い        |
| 型連携 | 弱い   | 強い        |
| 推奨  | △    | ◎         |

---

## まとめ

* `typeof` → 値から型を取得
* `keyof` → キーのUnionを取得
* `[]` → 型アクセス
* `as const` → リテラル型固定

---

### 最重要

```ts
typeof XXX[keyof typeof XXX]
```

→ 値のUnion型を生成する

---

## 補足

このパターンは以下で頻出：

* ルーティング
* エラーコード
* ステータス管理
* APIパラメータ
