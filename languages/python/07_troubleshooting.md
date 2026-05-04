# トラブルシュート

実務で頻出する問題と、その際に見るべきポイントを整理する。

---

# 基本方針

```text
事象 → 層の切り分け → 入出力確認 → 原因特定
````

---

# よくある問題と確認ポイント

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
Serializer（validation）
```

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
認証設定（Authentication）
```

---

## 403 Forbidden

### 主な原因

* 権限不足
* 認可ロジックエラー

---

### 見る場所

```text
permission（DRF）
↓
View
↓
Usecase / Domain の認可処理
```

---

## 404 Not Found

### 主な原因

* URLミス
* ID条件不一致

---

### 見る場所

```text
urls.py
↓
View
↓
Repositoryのfilter条件
```

---

## 500 Internal Server Error

### 主な原因

* 例外未処理
* 想定外の入力
* DBエラー

---

### 見る場所

```text
backendログ（stacktrace）
↓
View
↓
Usecase
↓
例外発生箇所
```

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

## データが更新されない

### 主な原因

* save / update漏れ
* transaction未commit
* Celeryに逃がしている

---

### 見る場所

```text
Usecase
↓
Repository save処理
↓
transaction
↓
Celery呼び出し有無
```

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
Redis接続
```

---

## パフォーマンスが遅い

### 主な原因

* N+1問題
* index不足
* 不要なJOIN

---

### 見る場所

```text
Repository query
↓
EXPLAIN
↓
select_related / prefetch_related
```

---

# 調査の進め方

## Step1

```text
Networkで事実確認
```

---

## Step2

```text
どの層の問題か切り分け
```

* Frontend
* API
* DB

---

## Step3

```text
入力と出力を比較
```

---

## Step4

```text
最後に正しかった地点を見つける
```

---

# よくあるアンチパターン

* ログを見ない
* Networkを見ない
* いきなりコードを読む
* 仮説を立てない
* フロントだけ / バックだけに原因を決めつける
