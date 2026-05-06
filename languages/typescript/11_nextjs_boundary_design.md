# Next.js Boundary Design

Next.js利用時のTypeScript境界設計を整理する。  
Next.jsそのものの仕組みではなく、**Server / Client境界で型をどう扱うか**に焦点を当てる。

---

## 概要

Next.jsでは、実行環境によってデータの扱い方が変わる。

```text
Server
 ↓
Client
````

TypeScript観点では、重要なのは以下。

* 外部データをそのままClientに渡さない
* Server側で検証・変換する
* Clientには表示・操作に必要な型だけ渡す
* propsはシリアライズ可能な形にする

---

## 1. Server / Client の基本

### App Router

App Routerでは、デフォルトはServer Component。

```tsx
export default async function Page() {
  return <div>Server Component</div>;
}
```

Client Componentにする場合は `"use client"` を付ける。

```tsx
"use client";

export function UserClient() {
  return <button onClick={() => alert("click")}>Click</button>;
}
```

---

### Pages Router

Pages Routerでは `"use client"` は不要。

```text
pages/
```

構成では、App RouterのようなServer Component / Client Componentの明確な分離はない。

---

## 2. TypeScript観点での境界

重要なのは、Router方式に関係なく以下の境界を意識すること。

```text
API / DB
 ↓
DTO
 ↓
Domain
 ↓
ViewModel
 ↓
Client props
```

---

## 3. 基本方針

### Clientに渡すもの

Client Componentには、UIに必要なデータだけを渡す。

```ts
type UserViewModel = {
  name: string;
  createdAtLabel: string;
};

type UserClientProps = {
  user: UserViewModel;
};
```

---

### 避けるもの

```ts
type BadProps = {
  createdAt: Date;
  onSubmitFromServer: () => void;
  userClass: User;
};
```

避ける理由：

* `Date` は文字列に変換した方が安全
* 関数はServerからClientへ渡せない
* クラスインスタンスはシリアライズに向かない

---

## 4. Server側の責務

Server側では、外部データを検証・変換する。

```ts
async function getUser(): Promise<UserViewModel> {
  const json = await fetchUserApi();

  const dto = UserSchema.parse(json);

  const user: User = {
    id: dto.user_id,
    name: dto.display_name,
    createdAt: new Date(dto.created_at),
  };

  return {
    name: user.name,
    createdAtLabel: user.createdAt.toLocaleDateString(),
  };
}
```

---

## 5. Client側の責務

Client側では、表示とイベント処理に集中する。

```tsx
"use client";

type Props = {
  user: UserViewModel;
};

export function UserClient({ user }: Props) {
  return (
    <button onClick={() => alert(user.name)}>
      {user.name}
    </button>
  );
}
```

---

## 6. 境界で渡してよい型

### 推奨

```ts
type Props = {
  id: string;
  name: string;
  age: number;
  isActive: boolean;
  tags: string[];
  profile: {
    label: string;
  };
};
```

### 基本方針

* primitive
* array
* plain object
* 表示用に整形済みの値

---

## 7. 境界で避ける型

```ts
type Props = {
  date: Date;
  callback: () => void;
  map: Map<string, string>;
  set: Set<string>;
  instance: UserClass;
};
```

### 理由

* シリアライズしづらい
* 実行環境をまたぐと扱いが不安定
* Client側の責務が重くなる

---

## 8. Pages Routerでの考え方

Pages RouterではServer / Clientの分離は弱い。

ただし、TypeScript設計としては以下を意識する。

```text
API
 ↓
Service
 ↓
ViewModel
 ↓
Page / Component
```

### 例

```tsx
export default function Page() {
  const [state, setState] = useState<AsyncState<UserViewModel>>({
    status: "loading",
  });

  useEffect(() => {
    getUser().then((data) => {
      setState({
        status: "success",
        data,
      });
    });
  }, []);

  if (state.status === "loading") return <Loading />;
  if (state.status === "error") return <Error />;

  return <UserClient user={state.data} />;
}
```

---

## 9. App Routerでの考え方

App RouterではServer / Client境界が明確になる。

```text
Server Component
 ↓
ViewModel
 ↓
Client Component props
```

### 例

```tsx
export default async function Page() {
  const user = await getUser();

  return <UserClient user={user} />;
}
```

---

## 10. Pages Router / App Router の比較

| 観点               | Pages Router | App Router |
| ---------------- | ------------ | ---------- |
| `"use client"`   | 不要           | 必要         |
| Server Component | なし           | あり         |
| 境界               | あいまい         | 明確         |
| TypeScript設計     | 任意で作る        | 作る意義が大きい   |
| 既存運用             | しやすい         | 移行コストあり    |
| 新規開発             | △            | ◎          |

---

## 11. 採用判断

### Pages Router継続でよいケース

* 既存プロダクトが安定稼働している
* 大規模な移行コストを避けたい
* チームがPages Routerに慣れている
* 短期改修が中心

---

### App Routerを検討するケース

* 新規開発
* 長期運用前提
* Server Componentを活かしたい
* layout / loading / error を整理したい
* データ取得をServer側に寄せたい

---

## 12. アンチパターン

### APIレスポンスをそのままClientへ渡す

```tsx
<UserClient user={apiResponse} />
```

---

### DTOをClientで直接使う

```tsx
user.display_name
```

---

### Client側で表示変換を大量に行う

```tsx
new Date(user.created_at).toLocaleDateString()
```

---

## 13. 設計原則

### 原則1：ClientにはViewModelを渡す

```text
Domain → ViewModel → Client props
```

---

### 原則2：外部仕様をClientに漏らさない

```tsx
user.display_name // 避ける
```

---

### 原則3：Server / Service側で検証・変換する

```text
unknown → DTO → Domain → ViewModel
```

---

### 原則4：propsはシリアライズ可能にする

```text
string / number / boolean / array / plain object
```

---

## まとめ

Next.js利用時のTypeScript境界設計では、以下を重視する。

* Clientには外部データをそのまま渡さない
* Server / Service側で検証・変換する
* Client propsはViewModelにする
* propsはシリアライズ可能な値にする
* Pages Routerでも型の境界は意識する
