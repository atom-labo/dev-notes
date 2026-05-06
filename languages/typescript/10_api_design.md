# API Design × TypeScript

TypeScriptを用いたAPI設計の実務パターンを整理する。  
本ファイルは「設計レイヤー」の中核。

---

## 概要

TypeScriptにおけるAPI設計は以下を扱う：

- レスポンス構造
- エラー設計
- 状態管理
- レイヤー分離（DTO / Domain / ViewModel）
- Service層設計

---

## 全体アーキテクチャ

```text
API
 ↓
DTO（外部仕様）
 ↓
Service層（変換・検証）
 ↓
Domain（内部モデル）
 ↓
ViewModel（表示用）
 ↓
AsyncState<T>
 ↓
UI
````

---

## 1. APIレスポンス設計

### 基本形（Discriminated Union）

```ts
type ApiResponse<T> =
  | { success: true; data: T }
  | { success: false; error: ApiError };
```

---

### ポイント

* successで分岐可能
* 型安全なNarrowingが効く

---

## 2. エラー設計

### エラー構造

```ts
type ApiError = {
  code: ErrorCode;
  message: string;
};
```

---

### エラーコード管理

```ts
const ERROR_CODE = {
  INTERNAL_ERROR: "INTERNAL_ERROR",
  NOT_FOUND: "NOT_FOUND",
} as const;

type ErrorCode =
  typeof ERROR_CODE[keyof typeof ERROR_CODE];
```

---

### ポイント

* messageは表示用
* 分岐はcodeで行う

---

## 3. 状態設計（AsyncState）

```ts
type AsyncState<T> =
  | { status: "loading" }
  | { status: "success"; data: T }
  | { status: "error"; error: ApiError };
```

---

### ポイント

* 状態は1つにまとめる
* Discriminated Unionで管理
* UI分岐が明確になる

---

## 4. DTO / Domain / ViewModel

---

### DTO（外部仕様）

```ts
type UserDto = {
  user_id: string;
  display_name: string;
  created_at: string;
};
```

---

### Domain（内部モデル）

```ts
type User = {
  id: string;
  name: string;
  createdAt: Date;
};
```

---

### ViewModel（表示用）

```ts
type UserViewModel = {
  name: string;
  createdAtLabel: string;
};
```

---

### 型の流れ

```text
DTO → Domain → ViewModel
```

---

### ポイント

* API仕様をUIに持ち込まない
* 表示ロジックを分離する

---

## 5. Service層

---

### 役割

* API呼び出し
* バリデーション（Zod）
* 型変換
* エラー変換

---

### 例

```ts
async function getUser(): Promise<AsyncState<UserViewModel>> {
  try {
    const json = await fetchUserApi();

    const dto = UserSchema.parse(json);

    const user: User = {
      id: dto.user_id,
      name: dto.display_name,
      createdAt: new Date(dto.created_at),
    };

    const viewModel: UserViewModel = {
      name: user.name,
      createdAtLabel: user.createdAt.toLocaleDateString(),
    };

    return {
      status: "success",
      data: viewModel,
    };
  } catch {
    return {
      status: "error",
      error: {
        code: "INTERNAL_ERROR",
        message: "取得失敗",
      },
    };
  }
}
```

---

## 6. UI層

```tsx
const state = await getUser();

if (state.status === "loading") return <Loading />;
if (state.status === "error") return <Error />;

return <div>{state.data.name}</div>;
```

---

### ポイント

* UIは状態のみを見る
* API仕様を知らない

---

## 7. Utility Types活用

---

### Partial（フォーム）

```ts
type UserForm = Partial<User>;
```

---

### Pick（必要な部分）

```ts
type UserName = Pick<User, "name">;
```

---

### Omit（除外）

```ts
type CreateUser = Omit<User, "id">;
```

---

### Record（マッピング）

```ts
const LABEL: Record<Status, string> = {
  success: "成功",
  error: "エラー",
};
```

---

## 8. as const / Record / satisfies

---

### 型生成

```ts
const STATUS = {
  SUCCESS: "success",
  ERROR: "error",
} as const;

type Status = typeof STATUS[keyof typeof STATUS];
```

---

### 制約

```ts
const LABEL = {
  success: "成功",
  error: "エラー",
} as const satisfies Record<Status, string>;
```

---

### 役割

| 技術        | 役割  |
| --------- | --- |
| as const  | 型生成 |
| Record    | 型制約 |
| satisfies | 型検証 |

---

## 9. フォーム設計

---

### Zodスキーマ

```ts
const UserFormSchema = z.object({
  name: z.string(),
  age: z.number(),
});
```

---

### 型生成

```ts
type UserForm = z.infer<typeof UserFormSchema>;
```

---

### ポイント

* 型とバリデーションを統一
* safeParseで検証

---

## 10. 設計原則

---

### 原則① 境界で変換する

```text
外部 → 内部
```

---

### 原則② 型はレイヤーごとに分ける

```text
DTO ≠ Domain ≠ ViewModel
```

---

### 原則③ 状態は1つにまとめる

```ts
AsyncState<T>
```

---

### 原則④ UIは表示に専念

---

### 原則⑤ 型＝設計

---

## 11. 判断基準

---

### Domainを分けるべき

* 複数画面で使う
* ロジックがある
* APIが複雑

---

### 分けなくてよい

* 小規模
* 一度しか使わない

---

## まとめ

* API設計は型設計そのもの
* Service層が設計の中核
* 型で安全性と保守性を担保する

---

## 一言

TypeScriptは「型を書く言語」ではなく
**「設計を強制する言語」**
