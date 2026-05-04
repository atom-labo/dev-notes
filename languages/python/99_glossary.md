# Glossary

Python / Django 関連の用語メモ。  
後から追記・整理していく前提。

---

# Python

## virtualenv / venv

Pythonの仮想環境。  
プロジェクトごとに依存関係を分離する仕組み。

---

## pyenv

Pythonのバージョン管理ツール。  
複数のPythonバージョンを切り替え可能。

---

## pip

Pythonのパッケージ管理ツール。

---

## requirements.txt

依存パッケージ一覧。  
再現性のある環境構築に利用。

---

## uv

高速なPythonパッケージ管理ツール。  
pip / venv の代替として利用されることもある。

---

## Ruff

Python用のLint / Formatterツール。

---

## typing

型ヒント機能。  
実行時ではなく静的解析で利用される。

---

## decorator

関数に機能を追加する仕組み。

---

## generator

`yield` を使った遅延評価の仕組み。

---

## async / await

非同期処理の構文。  
I/O待ち時間を有効活用する。

---

# Django

## Django

Python製のWebフレームワーク。  
ORMや認証などを標準提供。

---

## Django REST Framework（DRF）

DjangoでAPIを作るための拡張ライブラリ。

---

## Model

DBテーブル定義。

---

## ORM

データベース操作をPythonコードで行う仕組み。

---

## migration

DBスキーマ変更を管理する仕組み。

---

## View

リクエストを処理しレスポンスを返す部分。

---

## serializer

ModelとJSONの変換、およびバリデーションを行う。

---

## middleware

リクエスト/レスポンスの共通処理。

---

## request

HTTPリクエスト情報を持つオブジェクト。

---

## response

HTTPレスポンス。

---

## transaction.atomic

トランザクション管理。  
処理の一貫性を保証する。

---

# 非同期 / バックグラウンド処理

## Celery

バックグラウンドジョブ実行基盤。

---

## task

Celeryで実行される非同期処理単位。

---

## Redis

インメモリデータストア。  
Celeryのキューやキャッシュに使われる。

---

## worker

Celeryの処理実行プロセス。

---

## retry

失敗したタスクの再実行。

---

# パフォーマンス

## N+1問題

ループ内で追加クエリが発生し、DBアクセスが増える問題。

---

## select_related

外部キーをJOINでまとめて取得する。

---

## prefetch_related

別クエリで取得し、Python側で結合する。

---

## cache

計算結果を保存して再利用する仕組み。

---

# 運用

## Docker

アプリケーション実行環境をコンテナ化する仕組み。

---

## docker-compose

複数コンテナをまとめて管理するツール。

---

## gunicorn

Python用のWSGIサーバ。  
Djangoを本番環境で動かすために利用。

---

## WSGI

Python WebアプリとWebサーバを繋ぐ仕様。

---

## ASGI

非同期対応のインターフェース。

---

## nginx

リバースプロキシサーバ。  
HTTPSや静的ファイル配信を担当。

---

## CI/CD

コード変更を自動でテスト・デプロイする仕組み。

---

## GitHub Actions

GitHub上でCI/CDを実行する仕組み。

---

## environment variables

環境ごとに設定値を切り替える仕組み。

---

# 設計

## thin view

Viewにロジックを持たせず、最小限にする設計。

---

## fat service

ビジネスロジックをService層に集約する設計。

---

## idempotency

同じ処理を複数回実行しても結果が変わらない性質。

---

## immutable infrastructure

環境は作り直す前提で運用する考え方。

---
