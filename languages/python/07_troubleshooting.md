# トラブルシュート

実務で頻出する問題と、その際に確認すべきポイントのチェックリスト。

---

# 方針

```text
問題 → 原因パターン → 見る場所
````

---

# HTTPステータス別チェック

---

## 400 Bad Request

### 主な原因

* リクエストパラメータ不正
* 必須項目不足
* 型不一致

---

### 見る場所

```text
Network payload
↓
Serializer validation
```

---

### チェックポイント

* request.dataの構造
* 必須項目が揃っているか
* 型（string / int / null）
* serializerのvalidate処理

---

## 401 Unauthorized

### 主な原因

* 未ログイン
* トークン不正 / 期限切れ

---

### 見る場所

```text
request.user
↓
Authentication設定
```

---

### チェックポイント

* Authorizationヘッダ
* トークンの有効期限
* 認証方式（JWT / Session）

---

## 403 Forbidden

### 主な原因

* 権限不足
* 認可ロジック不備

---

### 見る場所

```text
permission（DRF）
↓
View
↓
Usecase / Domain
```

---

### チェックポイント

* request.userの権限
* 対象リソースとの関係
* permissionクラス
* ドメインの認可条件

---

## 404 Not Found

### 主な原因

* URLミス
* ID不一致
* データ未存在

---

### 見る場所

```text
urls.py
↓
View
↓
Repository filter
```

---

### チェックポイント

* path parameter
* filter条件
* soft delete（論理削除）
* ID型（int / uuid）

---

## 500 Internal Server Error

### 主な原因

* 未処理例外
* 想定外入力
* DBエラー

---

### 見る場所

```text
backendログ（stacktrace）
↓
Usecase
↓
例外箇所
```

---

### チェックポイント

* stacktraceの先頭エラー
* None参照
* transaction内処理
* 外部APIエラー

---

# 事象別チェック

---

## データが表示されない

### 主な原因

* APIレスポンスが空
* フロントのマッピングミス
* クエリ条件ミス

---

### 見る場所

```text
Network response
↓
Frontend mapping
↓
Serializer response
↓
Repository query
```

---

### チェックポイント

* responseの中身
* JSON構造
* filter条件
* select_related / prefetch_related

---

## データが更新されない

### 主な原因

* save / update漏れ
* transaction未commit
* 非同期処理

---

### 見る場所

```text
Usecase
↓
Repository save
↓
transaction
↓
Celery
```

---

### チェックポイント

* save呼び出し有無
* transaction.atomic
* Celeryに逃がしていないか
* 冪等性の問題

---

## 非同期処理（Celery）が動かない

### 主な原因

* Worker未起動
* Redis接続エラー
* task未登録

---

### 見る場所

```text
Celery workerログ
↓
task定義
↓
Redis
```

---

### チェックポイント

* workerプロセス
* キュー詰まり
* retry状況
* taskのimport漏れ

---

## パフォーマンスが遅い

### 主な原因

* N+1問題
* index不足
* 不要なクエリ

---

### 見る場所

```text
Repository query
↓
SQL（EXPLAIN）
↓
ORM使用方法
```

---

### チェックポイント

* query回数
* select_related / prefetch_related
* index有無
* 不要なループ内query

---

## ログが出ない / 追えない

### 主な原因

* logging設定不備
* request_id未付与

---

### 見る場所

```text
settings.LOGGING
↓
middleware
```

---

### チェックポイント

* logger設定
* handler
* request_id
* 出力形式（JSON）

---

# 調査の基本チェックリスト

---

## Step1

```text
Networkを確認したか？
```

---

## Step2

```text
ログを確認したか？
```

---

## Step3

```text
入力と出力を比較したか？
```

---

## Step4

```text
どの層の問題か切り分けたか？
```

---

## Step5

```text
最後に正しかった地点を特定したか？
```

---

# よくあるアンチパターン

* いきなりコードを読む
* Networkを見ない
* ログを見ない
* 仮説を立てない
* フロント / バックのどちらかに決めつける
