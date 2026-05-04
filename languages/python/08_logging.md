# ログ設計（logging）

実務でのログは「出す」ではなく「追跡できる」ことが目的。

---

# 方針

```text
ログ = 調査のための証拠
````

---

# 基本原則

```text
・構造化（JSON）
・一貫性
・追跡可能性
```

---

# ① loggingの基本

```python
import logging

logger = logging.getLogger(__name__)

logger.info("message")
logger.error("error occurred")
```

---

# ログレベル

| レベル     | 用途    |
| ------- | ----- |
| DEBUG   | 開発用詳細 |
| INFO    | 通常処理  |
| WARNING | 注意    |
| ERROR   | エラー   |

---

# 実務の使い分け

```text
INFO：通常のイベント
ERROR：例外・失敗
DEBUG：ローカルのみ
```

---

# ② 構造化ログ（重要）

## NG

```text
"user created"
```

---

## OK

```python
logger.info("user_created", extra={
    "user_id": user.id,
    "email": user.email,
})
```

---

## 理由

```text
検索・分析できる
```

---

# ③ 必須ログ項目

```text
request_id
user_id
path
method
status_code
```

---

## 例

```python
logger.info("request", extra={
    "request_id": request_id,
    "path": request.path,
    "method": request.method,
})
```

---

# ④ request_id（最重要）

## 役割

```text
1リクエストを追跡するID
```

---

## 流れ

```text
Frontend
↓
Backend（request_id付与）
↓
ログ
↓
Celery
```

---

## ポイント

```text
全ログに含める
```

---

# ⑤ Django logging設定

## 基本

```python
LOGGING = {
    "version": 1,
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
        }
    },
    "root": {
        "handlers": ["console"],
        "level": "INFO",
    },
}
```

---

## 方針

```text
stdoutに出す
```

---

## 理由

```text
Docker / Datadog前提
```

---

# ⑥ middlewareでのログ

## 用途

* request開始
* response終了

---

## イメージ

```python
def middleware(request):
    logger.info("request_start", extra={...})

    response = get_response(request)

    logger.info("request_end", extra={...})

    return response
```

---

# ⑦ エラーログ

## 例

```python
try:
    ...
except Exception as e:
    logger.error("error", extra={"error": str(e)})
    raise
```

---

## ポイント

```text
・握り潰さない
・必ず再raise
```

---

# ⑧ Celeryログ

## 注意

```text
非同期なので別トレースになる
```

---

## 対策

```python
logger.info("task_start", extra={
    "task_id": self.request.id,
    "user_id": user_id,
})
```

---

## ポイント

```text
request_idを引き継ぐと強い
```

---

# ⑨ ログ設計のパターン

---

## パターン①：イベントログ

```text
user_created
order_completed
```

---

## パターン②：エラーログ

```text
exception / failure
```

---

## パターン③：トレースログ

```text
request_start / request_end
```

---

# ⑩ よくある問題

---

## ❌ printデバッグ

---

## ❌ メッセージが曖昧

```text
"error occurred"
```

---

## ❌ request_idがない

---

## ❌ ログが多すぎる

---

## ❌ ログが少なすぎる

---

# ⑪ ログ設計のコツ

---

## コツ①

```text
検索できる形で出す
```

---

## コツ②

```text
誰が / 何を / いつ
```

---

## コツ③

```text
後から調査できる粒度
```

---

## コツ④

```text
重要な箇所だけ出す
```

---

# ⑫ Javaとの比較

| Java    | Django                 |
| ------- | ---------------------- |
| logback | logging                |
| MDC     | request_id             |
| AOPログ   | middleware / decorator |
| ELK     | Datadog                |
