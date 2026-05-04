# Django実務

Djangoを使った実務開発における設計・実装・運用の要点。

---

# 方針

```text
Djangoは「書く」より「どこに書くか」が重要
````

---

# アーキテクチャ（例）

```text
presentations（View / Serializer）
↓
usecases（処理の流れ）
↓
domains（業務ルール）
↓
infrastructures（DB / 外部API）
↓
models（ORM）
```

---

# レイヤー責務

| 層               | 役割               |
| --------------- | ---------------- |
| presentations   | HTTP入口           |
| serializers     | 入出力 / validation |
| usecases        | 処理の流れ            |
| domains         | ビジネスルール          |
| infrastructures | 技術実装             |
| models          | DB               |

---

# 処理フロー

```text
request
↓
View
↓
Serializer（validate）
↓
Usecase
↓
Domain
↓
Repository
↓
DB
↓
Response
```

---

# View設計

## 原則

```text
薄くする
```

---

## やること

* request受け取り
* serializer呼び出し
* usecase呼び出し
* response返却

---

## NG

```text
・ビジネスロジックを書く
・DB操作を書く
```

---

# Serializer設計

## 役割

```text
入力検証 + データ変換
```

---

## ポイント

```text
・必須/任意の定義
・型変換
・validate
```

---

## NG

```text
・DB操作
・外部API呼び出し
```

---

# Usecase設計（最重要）

## 役割

```text
処理の流れ（オーケストレーション）
```

---

## 例

```text
validate
↓
存在チェック
↓
保存
↓
非同期処理
```

---

## ポイント

```text
・transaction管理
・Domain呼び出し
・Repository呼び出し
```

---

# Domain設計

## 役割

```text
業務ルール
```

---

## 内容

* Entity
* ValueObject
* DomainService

---

## ポイント

```text
・Djangoに依存しない
・純粋Python
```

---

# Repository設計

## 役割

```text
DB操作の隠蔽
```

---

## ポイント

```text
・ORMを閉じ込める
・queryを集約する
```

---

# ORM設計（重要）

## 基本

```python
User.objects.filter(...)
```

---

## 注意点

```text
・N+1問題
・index設計
・不要なクエリ
```

---

## 対策

```python
select_related()
prefetch_related()
```

---

# transaction

## 基本

```python
from django.db import transaction

with transaction.atomic():
    ...
```

---

## ポイント

```text
・整合性保証
・Usecaseで管理
```

---

# 非同期処理（Celery）

## 使う場面

```text
・メール送信
・外部API
・重い処理
```

---

## 呼び出し

```python
task.delay(user_id)
```

---

## 重要

```python
transaction.on_commit(
    lambda: task.delay(user.id)
)
```

---

## 理由

```text
rollback時に実行されるのを防ぐ
```

---

# 冪等性（重要）

## 定義

```text
何回実行しても結果が同じ
```

---

## 例

```python
if not user.mail_sent:
    send_mail()
```

---

# API設計

## URL

```text
GET /users
POST /users
GET /users/{id}
```

---

## ポイント

```text
・名詞で表現
・一貫性
```

---

# エラーハンドリング

## 例

```json
{
  "code": "INVALID_PARAMETER",
  "message": "email is required"
}
```

---

## ポイント

```text
・形式を統一
・フロントが扱いやすくする
```

---

# pagination

## offset

```text
/users?page=1&limit=10
```

---

## cursor

```text
/users?cursor=xxx
```

---

## 実務

```text
大規模 → cursor推奨
```

---

# 設定（settings）

## 方針

```text
環境ごとに分離
```

---

## 内容

* DB
* SECRET_KEY
* logging
* 外部API

---

# logging

## 方針

```text
stdoutに出す（JSON）
```

---

## 理由

```text
・Docker
・Datadog
```

---

# テスト

## 優先順位

```text
1. Usecase
2. API
3. Model
```

---

## ポイント

```text
・外部依存はmock
・Celeryは同期化
```

---

# パフォーマンス

## チェックポイント

* N+1
* query数
* index
* cache

---

# セキュリティ

## 基本

```text
・認証（Authentication）
・認可（Permission）
```

---

## 注意

```text
ユーザーがアクセスして良いかを必ずチェック
```

---

# よくあるアンチパターン

* fat View
* fat Model
* Serviceなし
* Serializerにロジック
* transaction未使用
* Celery乱用

---

# Javaとの対応

| Java       | Django     |
| ---------- | ---------- |
| Controller | View       |
| Service    | Usecase    |
| Entity     | Domain     |
| Repository | Repository |
| JPA        | ORM        |
| MQ         | Celery     |
