# Troubleshooting

TypeScriptでよく発生するエラーと対処方法を整理する。  
実務での「詰まりポイント」を中心に扱う。

---

## 概要

- 型エラーは設計不整合のサイン
- エラー文を正しく読むことが重要
- 原因は大体パターン化される

---

## 1. Type 'X' is not assignable to type 'Y'

### 例

```ts
type User = {
  name: string;
};

const user: User = {
  name: 123, // エラー
};
````

---

### 原因

* 型の不一致

---

### 対処

* 型を合わせる
* または型定義を見直す

---

## 2. Object is possibly 'null'

### 例

```ts
const [user, setUser] = useState<User | null>(null);

user.name; // エラー
```

---

### 原因

* nullの可能性がある

---

### 対処

```ts id="w3q1pl"
if (!user) return;

user.name;
```

---

## 3. Property does not exist on type

### 例

```ts
type A = { a: string };
type B = { b: number };

function fn(x: A | B) {
  x.a; // エラー
}
```

---

### 原因

* Union型で確定していない

---

### 対処（Narrowing）

```ts id="n1h3zm"
if ("a" in x) {
  x.a;
}
```

---

## 4. Argument of type 'unknown'

### 例

```ts
function fn(data: unknown) {
  data.toUpperCase(); // エラー
}
```

---

### 原因

* unknownはそのまま使えない

---

### 対処

```ts id="k2j8pc"
if (typeof data === "string") {
  data.toUpperCase();
}
```

---

## 5. Type 'never'

### 例

```ts
function fn(value: never) {}
```

---

### 原因

* 到達不能な型
* 型の絞り込みミス

---

### よくあるケース

```ts id="x8w7vr"
type Status = "success";

if (status === "error") {
  // never
}
```

---

## 6. includesの型エラー

### 例

```ts
Object.values(ERROR_CODE).includes(code);
```

---

### エラー

```text
Argument of type 'unknown' is not assignable
```

---

### 対処

```ts id="j9k4vd"
Object.values(ERROR_CODE).includes(code as ErrorCode);
```

---

### 注意

* 型アサーションは最小限にする

---

## 7. 型推論が効かない

### 例

```ts
const ref = useRef(null);
```

---

### 問題

* 型が `null` になる

---

### 対処

```ts id="r8g3vm"
const ref = useRef<HTMLInputElement>(null);
```

---

## 8. 非同期処理の型

### 例

```ts
const data = await res.json();
```

---

### 問題

* 型が `any`

---

### 対処

```ts id="v4y2az"
const data: User = await res.json();
```

または

```ts id="m6k9xp"
const data = UserSchema.parse(await res.json());
```

---

## 9. 型と値のズレ

### 例

```ts
type Path = "/home";

const path = "/about";
```

---

### 問題

* 不整合

---

### 対処

```ts id="p1x7qs"
const PATHS = {
  HOME: "/home",
} as const;

type Path = typeof PATHS[keyof typeof PATHS];
```

---

## 10. anyの蔓延

### 問題

```ts
const data: any = ...
```

---

### 影響

* 型安全が崩壊

---

### 対処

* unknownを使う
* 型を定義する

---

## 11. React特有のエラー

---

### イベント型不一致

```ts id="f2m5xp"
function handle(e: Event) {}
```

---

### 対処

```ts id="b3n8ak"
function handle(e: React.MouseEvent<HTMLButtonElement>) {}
```

---

## 12. デバッグのコツ

---

### 型を確認

```ts id="n9t6zk"
type Debug = typeof value;
```

---

### hover確認

* IDEで型を確認する

---

### 一時的に分解

```ts id="d3r5ya"
const temp = value;
```

---

## まとめ

* エラーはパターン化できる
* 型のズレを疑う
* unknownは必ず絞る
* nullは常に意識

---

## 補足

トラブルシュート力は：

* 型理解
* 実務経験
* エラー慣れ

で決まる
