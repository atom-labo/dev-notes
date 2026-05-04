# Django実務

Djangoを実務で扱う際の設計・非同期処理・パフォーマンス・アーキテクチャの要点を整理する。

---

# 概要

実務では：

- Viewは薄く
- Serviceにロジック集約
- DBアクセス最適化
- 重い処理は非同期化

が基本方針。

---

# アーキテクチャ（fat service / thin view）

## 方針

```text
View：HTTPの入口（薄く）
Service：業務ロジック（厚く）
````

---

## 例（良い）

```python
class UserView(APIView):
    def post(self, request):
        serializer = UserSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        user = user_service.create_user(serializer.validated_data)
        return Response(UserSerializer(user).data)
```

---

## Service

```python
from django.db import transaction

@transaction.atomic
def create_user(data):
    user = User.objects.create(**data)

    transaction.on_commit(
        lambda: send_welcome_mail_task.delay(user.id)
    )

    return user
```

---

## ポイント

* Viewにビジネスロジックを書かない
* transactionはServiceで管理
* 非同期処理はon_commitで実行

---

# transaction

## 基本

```python
from django.db import transaction

@transaction.atomic
def func():
    ...
```

---

## ポイント

* DB操作の一貫性を保証
* rollbackされる可能性を考慮

---

## NG例

```python
with transaction.atomic():
    user.save()
    send_mail_task.delay()
```

---

## 正しい

```python
transaction.on_commit(
    lambda: send_mail_task.delay(user.id)
)
```

---

# Celery（非同期処理）

## 概要

```text
重い処理をバックグラウンドで実行
```

---

## 構成

```text
Django
 ↓
Redis（queue）
 ↓
Celery Worker
```

---

## task定義

```python
from celery import shared_task

@shared_task
def send_mail_task(user_id):
    ...
```

---

## 実行

```python
send_mail_task.delay(user.id)
```

---

## ポイント

* requestをブロックしない
* retry設計が重要
* 引数はprimitive（idなど）を渡す

---

# Redis

## 用途

* Celeryのqueue
* cache
* セッション

---

## cache例

```python
from django.core.cache import cache

data = cache.get("key")

if data is None:
    data = heavy_query()
    cache.set("key", data, 300)
```

---

## 注意点

* キャッシュの整合性
* invalidation設計

---

# パフォーマンスチューニング

## N+1問題

```python
users = User.objects.all()

for user in users:
    print(user.profile.name)
```

---

## 対策

### select_related

```python
User.objects.select_related("profile")
```

---

### prefetch_related

```python
User.objects.prefetch_related("groups")
```

---

## その他

* only / values でカラム絞り込み
* exists / count を適切に使う
* index設計

---

# async / await

## 概要

```text
I/O待ち時間を有効活用
```

---

## 例

```python
async def view(request):
    data = await external_api()
```

---

## 注意点

* CPU高速化ではない
* sync ORMと混在に注意
* Djangoはsync中心が多い

---

# middleware

## 概要

```text
横断的処理を共通化
```

---

## 例

* 認証
* logging
* トレースID
* ロケール

---

# logging（実務）

## 方針

```text
構造化ログ（JSON）を利用
```

---

## ポイント

* stdout出力
* request単位で追跡できるようにする
* Datadogなどと連携

---

# selector / repository

## selector

```text
参照専用クエリ
```

```python
def get_active_users():
    return User.objects.filter(active=True)
```

---

## repository

```text
ORM抽象化（必要に応じて）
```

---

## ポイント

* DjangoはORM直利用も多い
* 大規模では分離されることもある

---

# テスト（Django）

## APIテスト

```python
from rest_framework.test import APIClient

client = APIClient()
response = client.get("/users/")
```

---

## Celeryテスト

```python
CELERY_TASK_ALWAYS_EAGER = True
```

---

## ポイント

* Service単位でテスト
* 外部APIはmock

---

# よくあるアンチパターン

* fat view（ロジック詰め込み）
* transaction内で外部API
* N+1未対策
* Celeryでオブジェクト渡す
* グローバル状態依存

---

# Javaとの比較

| Java（Spring）   | Django             |
| -------------- | ------------------ |
| @Transactional | transaction.atomic |
| Service層       | service関数          |
| JPA            | ORM                |
| Message Queue  | Celery             |
| Redis Cache    | Django cache       |

---

# まとめ

* Viewは薄く、Serviceにロジック集約
* transactionと非同期の境界が重要
* N+1は最優先で対策
* Redis / Celeryは実務で必須
