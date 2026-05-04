# Django基礎

Djangoを「実務で使う」ための最小限の構造理解。

---

# 方針

```text
Django = HTTPリクエストを処理してレスポンスを返す仕組み
````

---

# 全体像

```text
Frontend
 ↓
HTTP
 ↓
Django
 ↓
DB（PostgreSQL）
```

---

# Djangoの役割

```text
・リクエスト受付
・処理（ロジック）
・レスポンス返却
```

---

# リクエスト処理の流れ（最重要）

```text
request
↓
urls.py
↓
View
↓
（Serializer / Usecase / ORM）
↓
response
```

---

# ① urls.py

## 役割

```text
URLとViewを紐付ける
```

---

## 例

```python
from django.urls import path
from .views import UserView

urlpatterns = [
    path("users/<int:id>/", UserView.as_view()),
]
```

---

## 実務ポイント

```text
・URLが入口
・まずここを見る
```

---

# ② View

## 役割

```text
requestを受けてresponseを返す
```

---

## 例

```python
from rest_framework.views import APIView
from rest_framework.response import Response

class UserView(APIView):
    def get(self, request, id):
        return Response({"id": id})
```

---

## 実務ポイント

```text
・処理は書かない（薄く）
・Usecaseへ委譲
```

---

# ③ requestオブジェクト

## よく使うもの

```python
request.method
request.data
request.query_params
request.user
```

---

## 実務ポイント

```text
・data = body
・query_params = URLパラメータ
```

---

# ④ Response

```python
return Response({"name": "Alice"})
```

---

## 実務ポイント

```text
・JSONで返す
・status codeを明示する
```

---

# ⑤ Django REST Framework（DRF）

API開発では必須。

---

## 役割

```text
・request/responseの扱い簡略化
・serializer
・validation
```

---

## request.data

```python
request.data
```

---

## Response

```python
return Response(data)
```

---

# ⑥ ORM（Model）

## 役割

```text
DB操作をPythonで行う
```

---

## 例

```python
user = User.objects.get(id=1)
```

---

## 作成

```python
User.objects.create(name="Alice")
```

---

## 実務ポイント

```text
・ORM = SQL
・パフォーマンスに直結
```

---

# ⑦ Model

## 例

```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
```

---

## 実務ポイント

```text
・DB定義そのもの
・migrationで管理
```

---

# ⑧ migration

## コマンド

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 実務ポイント

```text
DB変更 = コード変更
```

---

# ⑨ Serializer（DRF）

## 役割

```text
・入力検証
・JSON変換
```

---

## 例

```python
from rest_framework import serializers

class UserSerializer(serializers.Serializer):
    name = serializers.CharField()
```

---

## 実務ポイント

```text
・validationはここ
・DB操作は書かない
```

---

# ⑩ middleware

## 役割

```text
request / responseの共通処理
```

---

## 例

* 認証
* ログ
* CSRF

---

## 実務ポイント

```text
・軽く保つ
・横断処理のみ
```

---

# ⑪ authentication

```python
request.user
```

---

## 実務ポイント

```text
・ログインユーザー
・認可とは別
```

---

# ⑫ class-based view（CBV）

## 例

```python
class UserView(APIView):
    def get(self, request):
        ...
```

---

## 実務ポイント

```text
関数ベースより一般的
```

---

# ⑬ ステータスコード

| code | 意味           |
| ---- | ------------ |
| 200  | OK           |
| 201  | Created      |
| 400  | Bad Request  |
| 401  | Unauthorized |
| 403  | Forbidden    |
| 404  | Not Found    |

---

# ⑭ Javaとの対応

| Java（Spring） | Django     |
| ------------ | ---------- |
| Controller   | View       |
| Service      | Usecase    |
| Repository   | ORM        |
| Entity       | Model      |
| DTO          | Serializer |

---

# よくあるアンチパターン

* Viewにロジックを書く
* ORMを雑に使う
* migrationを理解していない
* SerializerでDB操作
* 認可を考慮していない
