# アーキテクチャ

## 概要

React / Next.jsにおけるアーキテクチャ設計は、

- 依存関係の整理
- 責務分離
- フレームワーク依存の隔離
- 再利用性

を目的とする。

---

## 基本思想

### 1. 依存方向は一方向

```txt
pages
↓
features
↓
shared
↓
libs
````

逆依存は禁止する。

---

### 2. フレームワーク依存の隔離

```txt
Next.js依存（pages / context）
→ 外側に閉じ込める
```

---

### 3. UIとロジックの分離

```txt
UI（Component）
ロジック（hooks / context）
データ取得（api）
```

---

## レイヤー構造（実務）

```txt
src/
├ pages
├ features/pages
├ shared-features
├ libs
├ parts
```

---

## 各レイヤーの役割

---

### pages（ルーティング層）

```txt
- URLと画面の紐付け
- getServerSidePropsの定義
- featuresへの委譲
```

---

### features/pages（画面単位）

```txt
- ページの本体
- 表示コンポーネント
- ページ固有ロジック
```

---

### shared-features（共通機能）

```txt
- API呼び出し
- 認証
- 共通ロジック
```

---

### libs（基盤）

```txt
- axios設定
- 共通ユーティリティ
```

---

### parts（UI部品）

```txt
- Button / Input
- Layout / Modal
```

---

## SSRパターン（重要）

```txt
request
↓
pages
↓
getServerSideProps
↓
データ取得関数（api）
↓
props
↓
Component
```

---

### ポイント

* SSR時にデータ取得
* Componentはpropsのみ受け取る
* ComponentでAPI通信しない

---

## Client初期化パターン

```txt
Component
↓
useEffect / custom hook
↓
初期化処理
↓
API取得
↓
state更新
↓
描画
```

---

### 用途

* 認証が必要なページ
* クライアント依存の処理（localStorageなど）

---

## API層設計

```txt
Component
↓
hooks
↓
api.ts
↓
httpClient（axios）
↓
Backend API
```

---

### ルール

* 画面から直接axiosを呼ばない
* 1API = 1関数
* 型を定義する
* APIパスは定数化

---

## axios設計

```txt
httpClient
httpClientWithCredentials
```

---

### 役割

* httpClient → 認証不要
* httpClientWithCredentials → 認証付き

---

### interceptor

```txt
request → ヘッダー付与
response → エラー処理
```

---

## コンポーネント設計

```txt
Container（ロジック）
Presentational（UI）
```

---

### 原則

* UIはpropsのみで動く
* APIを直接呼ばない

---

## 依存ルール

```txt
OK
pages → features → shared → libs

NG
shared → features
features → pages
```

---

## アンチパターン

### pages肥大化

```txt
ロジックを全部書く
```

---

### UIにAPIを書く

```txt
axiosを直接呼ぶ
```

---

### 逆依存

```txt
shared → features
```

---

## Vueとの対応

| Vue         | React              |
| ----------- | ------------------ |
| pages       | pages              |
| asyncData   | getServerSideProps |
| composables | hooks              |
| service     | api層               |

---

## まとめ

* 依存方向の設計が最重要
* SSRとClient初期化を使い分ける
* API層を分離する
* フレームワーク依存を外側に閉じ込める
