# Django運用

Djangoアプリケーションを実務で安定稼働させるための運用知識。

---

# 方針

```text
「動く」より「止まらない・追跡できる」
````

---

# 全体構成（典型）

```text
Client（Frontend）
 ↓
Nginx（リバースプロキシ）
 ↓
Gunicorn（WSGIサーバ）
 ↓
Django
 ↓
DB（PostgreSQL）
 ↓
Redis（Celery / Cache）
```

---

# ① WSGI / ASGI

## WSGI

```text
同期処理（標準）
```

---

## ASGI

```text
非同期対応（WebSocketなど）
```

---

## 実務

```text
API中心ならWSGIで十分なケースが多い
```

---

# ② Gunicorn

## 役割

```text
Djangoを本番で動かすサーバ
```

---

## 起動例

```bash
gunicorn config.wsgi:application --workers 4 --bind 0.0.0.0:8000
```

---

## パラメータ

| 項目      | 意味    |
| ------- | ----- |
| workers | プロセス数 |
| bind    | ポート   |

---

## 実務ポイント

```text
・CPUコア数に応じて調整
・worker不足 → 遅延
・worker過多 → メモリ枯渇
```

---

# ③ Nginx

## 役割

```text
・リバースプロキシ
・SSL終端
・静的ファイル配信
```

---

## 流れ

```text
Client → Nginx → Gunicorn → Django
```

---

# ④ 静的ファイル

## collectstatic

```bash
python manage.py collectstatic
```

---

## 配信

```text
Nginxから配信
```

---

## ポイント

```text
Djangoで配信しない（本番）
```

---

# ⑤ 環境変数

## 基本

```python
import os

SECRET_KEY = os.getenv("SECRET_KEY")
```

---

## 管理対象

* DB接続
* APIキー
* SECRET_KEY

---

## ポイント

```text
コードにハードコードしない
```

---

# ⑥ logging

## 方針

```text
stdoutに出力
```

---

## 理由

```text
・Docker前提
・ログ集約（Datadog / CloudWatch）
```

---

## 必須情報

```text
request_id
user_id
path
status_code
```

---

# ⑦ 監視

## 観点

```text
・エラー率
・レスポンスタイム
・トラフィック
```

---

## ツール例

* Datadog
* CloudWatch
* Sentry

---

## ポイント

```text
「異常に気づける」ことが重要
```

---

# ⑧ DB運用

## 観点

* index
* slow query
* connection数

---

## ポイント

```text
ORMでもSQLを意識する
```

---

# ⑨ マイグレーション

## コマンド

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 注意

```text
・本番での適用順序
・ロールバック考慮
```

---

# ⑩ Celery運用

## 構成

```text
Django
 ↓
Redis（broker）
 ↓
Celery Worker
```

---

## 起動

```bash
celery -A config worker -l info
```

---

## 注意

```text
・Workerが落ちると処理されない
・キュー詰まりに注意
```

---

# ⑪ 非同期の注意点

* retry前提で設計
* 冪等性を確保
* 引数はIDのみ

---

# ⑫ デプロイ

## 流れ（例）

```text
コード更新
↓
ビルド（Docker）
↓
migration
↓
アプリ再起動
```

---

## ポイント

```text
・migrationとコードの整合性
・ダウンタイム最小化
```

---

# ⑬ セキュリティ

## 基本設定

```python
DEBUG = False
SECURE_SSL_REDIRECT = True
```

---

## 注意

```text
・SECRET漏洩
・CORS設定
・認可漏れ
```

---

# ⑭ パフォーマンス

## チェック

* N+1
* キャッシュ
* DB負荷

---

## 対策

```text
・Redis cache
・query最適化
```

---

# ⑮ キャッシュ

## 例

```text
・Redis
```

---

## 用途

* APIレスポンス
* セッション

---

# ⑯ トラブル対応の基本

```text
1. ログを見る
2. Networkを見る
3. 再現する
4. 原因特定
```

---

# よくある障害

* 500エラー増加
* DB接続枯渇
* Celery停止
* メモリ不足
* ログ肥大

---

# Javaとの対応

| Java         | Django   |
| ------------ | -------- |
| Tomcat       | Gunicorn |
| Apache/Nginx | Nginx    |
| Spring Boot  | Django   |
| MQ           | Celery   |
| APM          | Datadog  |
