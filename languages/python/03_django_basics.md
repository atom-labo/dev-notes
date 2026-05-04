# Django基礎

Djangoの基本概念とWebアプリとしての流れを整理する。

---

# 概要

Djangoは：

- Python製のWebフレームワーク
- ORM・認証・管理画面などを標準提供
- 高速にWebアプリを構築できる

---

# Djangoの位置づけ

```text
Frontend（React / Next.js）
    ↓ HTTP
Django（Backend）
    ↓
PostgreSQL
````

---

# Djangoの特徴

* batteries included（全部入り）
* ORM標準搭載
* 管理画面あり
* 認証機能あり

---

# Djangoの構成（基本）

```text
Model
View
Template
```

---

## 役割

| 要素       | 内容      |
| -------- | ------- |
| Model    | DB定義    |
| View     | リクエスト処理 |
| Template | HTML表示  |

※ API開発ではTemplateはほぼ使わない

---

# request / response の流れ

かなり重要。

---

```text
Browser
 ↓
URL routing
 ↓
View
 ↓
ORM / Service
 ↓
Response(JSON)
```

---

# request オブジェクト

## 概要

```text
HTTPリクエストの情報を持つ
```

---

## よく使うもの

```python
request.method
request.GET
request.POST
request.user
request.headers
```

---

# URL routing

## urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path("users/<int:id>/", views.get_user),
]
```

---

## 役割

```text
URLとViewを紐付ける
```

---

# View

## 基本

```python
def get_user(request, id):
    return JsonResponse(...)
```

---

## 役割

```text
requestを受けてresponseを返す
```

---

# Django REST Framework（DRF）

API開発ではほぼ必須。

---

## Response

```python
from rest_framework.response import Response

return Response({"name": "Alice"})
```

---

## request.data

```python
request.data
```

JSON bodyを取得。

---

# ORM

## 概要

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

## フィルタ

```python
User.objects.filter(active=True)
```

---

# Model

## 例

```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
```

---

## ポイント

* DBテーブルに対応
* migrationで管理

---

# migration

## 作成

```bash
python manage.py makemigrations
```

---

## 適用

```bash
python manage.py migrate
```

---

## ポイント

```text
DB変更 = コード変更
```

---

# serializer（DRF）

## 概要

```text
Model ⇄ JSON 変換 + validation
```

---

## 例

```python
from rest_framework import serializers

class UserSerializer(serializers.Serializer):
    name = serializers.CharField()
```

---

## 利用

```python
serializer = UserSerializer(data=request.data)
serializer.is_valid()
```

---

# middleware

## 概要

```text
request / response の共通処理
```

---

## イメージ

```text
request
 ↓
middleware
 ↓
view
 ↓
middleware
 ↓
response
```

---

## 例

* 認証
* ログ
* CSRF
* トレース

---

# authentication

## request.user

```python
request.user
```

ログインユーザー情報。

---

# class-based view（CBV）

## 例

```python
from rest_framework.views import APIView

class UserView(APIView):
    def get(self, request):
        return Response(...)
```

---

## ポイント

* 関数ベースより拡張しやすい
* 実務でよく使う

---

# exception handling

## 例

```python
raise ValidationError("error")
```

---

## 動作

```text
例外 → HTTPレスポンスへ変換
```

---

# status code

| code | 意味           |
| ---- | ------------ |
| 200  | OK           |
| 201  | Created      |
| 400  | Bad Request  |
| 401  | Unauthorized |
| 404  | Not Found    |

---

# Djangoの役割まとめ

```text
HTTP requestを受けて
↓
DBやロジックを処理し
↓
HTTP responseを返す
```

---

# Javaとの比較

| Java（Spring） | Django     |
| ------------ | ---------- |
| Controller   | View       |
| Service      | （別途実装）     |
| Repository   | ORM        |
| Entity       | Model      |
| DTO          | Serializer |

---

# 注意点

* Viewにロジックを書きすぎない
* ORMの使い方で性能が変わる
* migration管理は重要
* serializerはAPIの境界

---

# まとめ

* DjangoはWebアプリの土台
* request → view → response の流れを理解する
* ORMとserializerが重要
* DRFを使うのが基本
