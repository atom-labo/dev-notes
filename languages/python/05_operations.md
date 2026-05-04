# 運用・CI/CD

Djangoアプリを実務で運用するための基盤・デプロイ・CI/CDの要点を整理する。

---

# 概要

運用では：

- 実行環境の再現（Docker）
- 安定したWebサーバ（gunicorn / ASGI）
- 自動テスト・自動デプロイ（CI/CD）
- 設定の外出し（環境変数）
- 監視・ログ

が重要。

---

# Docker

## 概要

```text
実行環境ごとコンテナ化する
````

---

## 目的

* 環境差分の排除
* 依存関係の固定
* ローカルと本番の一致

---

## Django構成イメージ

```text
Browser
 ↓
nginx
 ↓
gunicorn / uvicorn
 ↓
Django
 ↓
PostgreSQL

Redis
Celery
```

---

## Dockerfile（例）

```dockerfile
FROM python:3.12

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "manage.py", "runserver"]
```

---

## docker-compose（例）

```yaml
services:
  web:
  db:
  redis:
  worker:
```

---

## volume

```yaml
volumes:
  - .:/app
```

---

## ポイント

* ローカルコードと同期
* ホットリロード可能

---

# 環境変数

## 例

```python
import os

SECRET_KEY = os.getenv("SECRET_KEY")
```

---

## 管理対象

* DB接続情報
* APIキー
* SECRET_KEY

---

## ポイント

* コードに書かない
* 環境ごとに切り替える

---

# migration

## 実行

```bash
python manage.py migrate
```

---

## ポイント

```text
DB変更もコードの一部
```

---

## 注意

* deploy順序に注意
* backward互換を意識

---

# collectstatic

```bash
python manage.py collectstatic
```

---

## 役割

* 静的ファイル集約
* nginxから配信

---

# gunicorn

## 概要

```text
WSGIサーバ（本番用）
```

---

## 起動

```bash
gunicorn config.wsgi:application
```

---

## worker

```bash
gunicorn -w 4 config.wsgi
```

---

## ポイント

* runserverは本番NG
* worker数調整が重要

---

# WSGI / ASGI

## WSGI

```text
同期処理用インターフェース
```

---

## ASGI

```text
非同期対応（WebSocketなど）
```

---

## サーバ例

| 種類   | サーバ              |
| ---- | ---------------- |
| WSGI | gunicorn         |
| ASGI | uvicorn / daphne |

---

# nginx

## 役割

* reverse proxy
* HTTPS
* static配信
* load balancing

---

## 構成

```text
Browser
 ↓
nginx
 ↓
gunicorn
 ↓
Django
```

---

# Celery運用

## 構成

```text
web
worker
beat
```

---

## ポイント

* workerは別コンテナ
* Redisがbroker
* retry設計が重要

---

# CI/CD

## 概要

```text
コード変更を自動で検証・デプロイ
```

---

## 流れ

```text
git push
 ↓
CI
 ↓
lint / test / build
 ↓
CD
 ↓
deploy
```

---

# CIでやること

## lint

```bash
ruff check .
```

---

## format check

```bash
ruff format --check .
```

---

## test

```bash
pytest
```

---

## type check

```bash
mypy
```

---

## security

```bash
pip-audit
```

---

## migration check

```bash
python manage.py makemigrations --check
```

---

# GitHub Actions

## 例（概要）

```yaml
name: CI

on:
  push:

jobs:
  test:
    steps:
      - run: pytest
```

---

## ポイント

* 自動実行
* pull requestでチェック

---

# uv（CIでの利用）

```bash
uv sync --frozen
uv run pytest
```

---

## ポイント

* 高速
* 再現性高い

---

# Docker build（CI）

```bash
docker build .
```

---

## 目的

* buildエラー検知
* 本番環境再現

---

# デプロイ時の重要ポイント

## migrationタイミング

```text
アプリとDBの整合性
```

---

## healthcheck

```text
起動確認
```

---

## rollback

```text
失敗時に戻せる設計
```

---

# ログ

## 方針

```text
stdoutへ出力
```

---

## 理由

* Dockerとの相性
* 集約しやすい

---

## 連携

* Datadog
* CloudWatch

---

# immutable infrastructure

## 概要

```text
コンテナは作り直す前提
```

---

## ポイント

* 手作業変更しない
* stateは外部へ

---

# よくあるアンチパターン

* runserverを本番で使う
* migration忘れ
* env変数未設定
* container内で手作業
* DB接続先をlocalhostにする

---

# Javaとの比較

| Java            | Django         |
| --------------- | -------------- |
| Tomcat          | gunicorn       |
| Servlet         | WSGI           |
| Spring Boot jar | Docker image   |
| Jenkins         | GitHub Actions |
| Redis           | Redis          |

---

# まとめ

* Dockerで環境を統一
* gunicornで本番実行
* CI/CDで品質担保
* 環境変数で設定管理
* migrationは最重要
