# TypeScript

TypeScriptの学習内容を、実務で使えるナレッジとして整理する。  
主に **React（Next.js）との併用** を前提とした内容を扱う。

---

## 概要

TypeScriptは、JavaScriptに**静的型システムを追加した言語**。

- コンパイル時に型チェックが可能
- バグの早期検出
- IDE補完の強化
- 設計（特にAPI/I/F）との親和性が高い

---

## 目的

- JavaScriptとの差分をベースに理解する
- 型安全な設計を実現する
- React / Next.jsで実務利用できるレベルにする

---

## 学習方針

- 「動く」よりも「型で保証する」を重視
- 型は**設計の一部**として扱う
- 実務パターンをベースに整理する

---

## ディレクトリ構成

```text
typescript/
├ 01_typescript_basics.md
├ 02_type_system.md
├ 03_type_operations.md
├ 04_functions_and_generics.md
├ 05_object_types.md
├ 06_react_nextjs_types.md
├ 07_api_and_runtime_validation.md
├ 08_patterns.md
├ 09_troubleshooting.md
└ 99_glossary.md
````

---

## 各ファイル

### 基礎

* [01_typescript_basics.md](./typescript/01_typescript_basics.md)
  TypeScriptの基本、JavaScriptとの違い、tsconfig、strict

* [02_type_system.md](./typescript/02_type_system.md)
  基本型、Union、Literal、any / unknown / never、Narrowing

---

### 型操作（重要）

* [03_type_operations.md](./typescript/03_type_operations.md)
  typeof / keyof / indexed access / as const / 型生成パターン

---

### 関数・ジェネリクス

* [04_functions_and_generics.md](./typescript/04_functions_and_generics.md)
  関数型、戻り値、ジェネリクス、型制約（extends）

---

### オブジェクト・設計

* [05_object_types.md](./typescript/05_object_types.md)
  type / interface、構造定義、readonly、Record

---

### React / Next.js

* [06_react_nextjs_types.md](./typescript/06_react_nextjs_types.md)
  props、children、event、useState、useRef、カスタムHook

---

### API / バリデーション

* [07_api_and_runtime_validation.md](./typescript/07_api_and_runtime_validation.md)
  APIレスポンス型、型ガード、Zod、実行時検証

---

### 実務パターン

* [08_patterns.md](./typescript/08_patterns.md)
  定数管理、エラーコード、ルーティング、型生成パターン

---

### トラブルシュート

* [09_troubleshooting.md](./typescript/09_troubleshooting.md)
  型エラーの読み方、よくあるエラーと対処

---

### 用語

* [99_glossary.md](./typescript/99_glossary.md)
  用語整理（Union型、型ガード、ジェネリクス等）

---

## 補足

TypeScriptは単なる言語ではなく、**設計ツール**として扱う。

特に以下と強く結びつく：

* API設計（フロント / バック間I/F）
* 状態管理
* バリデーション
* ドメインモデル

---

## 優先理解ポイント

1. 型システム（Union / unknown / Narrowing）
2. 型操作（typeof / keyof / as const）
3. ジェネリクス
4. Reactでの型運用
5. APIとの連携

---

## 方針

* 実務で使うパターンを中心に蓄積
* 現場で得た知見を随時反映
