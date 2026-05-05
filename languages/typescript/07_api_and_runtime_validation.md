# API and Runtime Validation

API連携と実行時バリデーションを整理する。  
TypeScript単体では保証できない部分を補完するのが目的。

---

## 概要

TypeScriptは**コンパイル時の型チェックのみ**。

→ 実行時（APIレスポンスなど）は保証されない

---

## 問題

```ts
type User = {
  id: number;
  name: string;
};

const user: User = await res.json();
````

---

### 問題点

* JSONの中身が違ってもエラーにならない
* 実行時にバグになる可能性あり

---

## 1. 型だけでは不十分

| 層          | 役割         |
| ---------- | ---------- |
| TypeScript | コンパイル時チェック |
| 実行時        | 無保証        |

---

## 2. 解決方法①：型ガード

---

### 基本

```ts
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null;
}
```

---

### 使用例

```ts
const data = await res.json();

if (!isUser(data)) {
  throw new Error("Invalid data");
}

const user = data;
```

---

### 問題点

* 手書きが面倒
* 厳密な検証が難しい

---

## 3. 解決方法②：スキーマ検証（推奨）

Zod を使用

---

### 基本

```ts
import { z } from "zod";

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
});
```

---

### 使用

```ts
const data = await res.json();
const user = UserSchema.parse(data);
```

---

### メリット

* 実行時に検証される
* 型も自動生成される
* 保守性が高い

---

## 4. 型生成

```ts
type User = z.infer<typeof UserSchema>;
```

---

### ポイント

* スキーマから型を生成
* 二重管理を防ぐ

---

## 5. Next.jsでの利用

---

### Server Component

```ts
export default async function Page() {
  const res = await fetch("...");
  const data = await res.json();

  const user = UserSchema.parse(data);

  return <div>{user.name}</div>;
}
```

---

### API Route（入力検証）

```ts
export async function POST(req: Request) {
  const body = await req.json();

  const parsed = UserSchema.parse(body);

  return Response.json(parsed);
}
```

---

## 6. エラーハンドリング

```ts
try {
  const user = UserSchema.parse(data);
} catch (e) {
  // バリデーションエラー
}
```

---

## 7. 安全なfetchパターン

```ts
async function fetchUser(): Promise<User> {
  const res = await fetch("/api/user");
  const data = await res.json();

  return UserSchema.parse(data);
}
```

---

## 8. よくあるパターン

---

### APIレスポンス構造

```ts
const ApiResponseSchema = z.object({
  data: UserSchema,
});
```

---

### 配列

```ts
const UsersSchema = z.array(UserSchema);
```

---

### Optional

```ts
const UserSchema = z.object({
  name: z.string().optional(),
});
```

---

## 9. アンチパターン

---

### 型だけ定義

```ts
type User = {
  id: number;
};
```

→ 実行時に保証なし

---

### any使用

```ts
const data: any = await res.json();
```

---

## 10. 本質

TypeScript単体では：

* 「型が正しい前提」でしか動かない

---

### 必要な構成

* 型（TypeScript）
* バリデーション（Zod）

---

## まとめ

* APIは型だけでは不十分
* 実行時バリデーションが必要
* Zodで型と検証を統一する

---

## 補足

この考え方は以下にも応用：

* フォームバリデーション
* クエリパラメータ
* localStorage
