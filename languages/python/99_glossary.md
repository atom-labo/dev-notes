# 用語集（Glossary）

Django / Python / Web開発で頻出する用語の整理。

---

# 基本用語

---

## API

```text
アプリケーション間のインターフェース
````

---

## REST

```text
リソース指向のAPI設計
```

---

## HTTP

```text
クライアントとサーバ間の通信プロトコル
```

---

## JSON

```text
データ交換フォーマット
```

---

# Django関連

---

## View

```text
HTTPリクエストを受けてレスポンスを返す入口
```

---

## Serializer

```text
入力検証とデータ変換を行う
```

---

## Model

```text
DBテーブルの定義（ORM）
```

---

## ORM

```text
DB操作をPythonコードで行う仕組み
```

---

## urls.py

```text
URLとViewの対応を定義
```

---

## migration

```text
DB変更をコードで管理
```

---

# アーキテクチャ

---

## Usecase

```text
処理の流れを定義する層
```

---

## Domain

```text
ビジネスルールを持つ層
```

---

## Entity

```text
業務上のデータ構造（IDを持つ）
```

---

## Value Object

```text
値そのものを表すオブジェクト（不変）
```

---

## Repository

```text
DB操作を抽象化する層
```

---

## Infrastructure

```text
DB / 外部APIなど技術的実装
```

---

## Presentation

```text
HTTPインターフェース（View / Serializer）
```

---

# 非同期・処理

---

## Celery

```text
バックグラウンド処理を行うタスクキュー
```

---

## Worker

```text
Celeryタスクを実行するプロセス
```

---

## Broker

```text
タスクをキューイングする仕組み（Redisなど）
```

---

## 冪等性（Idempotency）

```text
何回実行しても結果が同じになる性質
```

---

## transaction

```text
DBの整合性を保つ処理単位
```

---

# API設計

---

## ステータスコード

```text
HTTPの結果を示すコード（200 / 400 / 500など）
```

---

## pagination

```text
データを分割して取得する仕組み
```

---

## offset方式

```text
ページ番号で取得
```

---

## cursor方式

```text
位置ベースで取得（大規模向け）
```

---

## idempotency

```text
同じ操作を複数回行っても結果が同じ
```

---

# Python関連

---

## typing

```text
型ヒント（実行時には強制されない）
```

---

## mutable

```text
変更可能なオブジェクト（list / dict）
```

---

## immutable

```text
変更不可（int / str）
```

---

## decorator

```text
関数に処理を追加する仕組み
```

---

## generator

```text
遅延評価で値を生成する
```

---

## async / await

```text
I/O待ち時間を効率化する仕組み
```

---

# 運用

---

## Gunicorn

```text
Djangoを本番で動かすWSGIサーバ
```

---

## Nginx

```text
リバースプロキシ / 静的ファイル配信
```

---

## logging

```text
ログ出力の仕組み
```

---

## request_id

```text
1リクエストを追跡するためのID
```

---

## monitoring

```text
システムの状態を監視する仕組み
```

---

# 調査・デバッグ

---

## Network

```text
ブラウザの通信ログ（DevTools）
```

---

## stacktrace

```text
例外発生時の呼び出し履歴
```

---

## N+1問題

```text
不要なDBクエリが大量発生する問題
```
