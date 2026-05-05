# アーキテクチャ

## 概要

React / Next.jsのアーキテクチャは、

```txt
責務分離
依存関係の整理
フレームワーク依存の隔離
再利用性
````

を目的とする。

---

# 基本思想

## 1. 依存方向は一方向

```txt
pages
↓
features
↓
shared
↓
libs
```

---

## 2. フレームワーク依存の隔離

```txt
Next.js依存（pages / context）
→ 外側に閉じ込める
```

---

## 3. UIとロジックの分離

```txt
UI（Component）
ロジック（hooks）
データ取得（api）
```

---

# レイヤー構造例

```txt
src/
├ pages
├ features/pages
├ shared-features
├ libs
├ parts
```

---

# 各レイヤーの役割

---

## pages（ルーティング層）

```txt
- URLと画面の紐付け
- getServerSidePropsの定義
- featuresへ委譲
```

---

## features/pages（画面単位）

```txt
- ページの本体
- 表示コンポーネント
- ページ固有ロジック
```

---

## shared-features（共通機能）

```txt
- API呼び出し
- 認証
- 共通ロジック
```

---

## libs（基盤）

```txt
- axios設定
- 共通ユーティリティ
```

---

## parts（UI部品）

```txt
- Button / Input
- Layout / Modal
```

---

# SSRパターン

```txt
request
↓
pages
↓
getServerSideProps
↓
API取得
↓
props生成
↓
Component
```

---

## ポイント

```txt
ComponentでAPIを呼ばない
props駆動にする
```

---

# Client初期化パターン

```txt
Component
↓
usePageInitialization
↓
認証チェック
↓
API取得
↓
state更新
↓
描画
```

---

## 用途

```txt
- 認証ページ
- localStorage依存
```

---

# 認証・セッション設計

## 前提

```txt
localStorage / Cookie / メモリ
```

---

## Client認証パターン

```txt
Page
↓
初期化Hook
↓
getCurrentUser
↓
成功 → 続行
失敗 → ログインへ
```

---

## axiosとの連携

```txt
request → 認証ヘッダー付与
response → 401でリダイレクト
```

---

## SSRとの関係

```txt
localStorageはサーバーで使えない
→ 認証はClientで行う
```

---

# API層設計

```txt
Component
↓
hooks
↓
api.ts
↓
httpClient
```

---

## ルール

```txt
- 1API = 1関数
- UIからaxiosを呼ばない
- 型を定義する
```

---

# axios設計

```txt
httpClient
httpClientWithCredentials
```

---

## 役割

```txt
httpClient → 公開API
httpClientWithCredentials → 認証API
```

---

## interceptor

```txt
request → ヘッダー付与
response → エラー処理
```

---

# データ変換（Presenter）

```txt
APIレスポンス
↓
UI用データに変換
↓
Component
```

---

## ポイント

```txt
API構造をUIに持ち込まない
```

---

# コンポーネント設計

```txt
Container（ロジック）
↓
Presentational（UI）
```

---

## 原則

```txt
UIはpropsのみで動く
```

---

# 依存ルール

```txt
OK
pages → features → shared → libs

NG
shared → features
features → pages
```

---

# アンチパターン

---

## ❌ pages肥大化

```txt
ロジックを全部書く
```

---

## ❌ UIにAPIを書く

```txt
axios直書き
```

---

## ❌ 逆依存

```txt
shared → features
```

---

# Vueとの対応

| Vue         | React              |
| ----------- | ------------------ |
| pages       | pages              |
| asyncData   | getServerSideProps |
| composables | hooks              |
| service     | api層               |

---

# まとめ

* 依存方向の設計が最重要
* SSRとClient初期化を使い分ける
* API層を分離する
* 認証はClientで扱うことが多い
* Presenter層でUIとデータを分離する
