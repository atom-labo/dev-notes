# Python実務

実務で頻出する機能・設計パターン・注意点の整理。

---

# 方針

```text
「動くコード」ではなく「壊れにくいコード」を書く
````

---

# typing（型ヒント）

## 基本

```python
def func(x: int) -> str:
    return str(x)
```

---

## 実務でよく使う型

```python
from typing import List, Dict

def func(x: list[int]) -> dict[str, int]:
    ...
```

---

## Optional

```python
def func(x: int | None) -> int:
    ...
```

---

## 実務ポイント

```text
・API入出力は必須レベル
・Service層は強く推奨
・型が仕様書の代わりになる
```

---

# logging

## 基本

```python
import logging

logger = logging.getLogger(__name__)

logger.info("message")
```

---

## レベル

| レベル     | 用途  |
| ------- | --- |
| DEBUG   | 詳細  |
| INFO    | 通常  |
| WARNING | 注意  |
| ERROR   | エラー |

---

## 実務ポイント

```text
・printは使わない
・構造化ログ（JSON）前提で設計
・request単位で追跡できるようにする
```

---

# exception（例外）

## 基本

```python
try:
    ...
except ValueError:
    ...
```

---

## raise

```python
raise ValueError("invalid")
```

---

## 実務ポイント

```text
・例外は握り潰さない
・適切な層で処理する
・ドメイン例外とシステム例外を分ける
```

---

## NG

```python
except Exception:
    pass
```

---

# import / module設計

## 基本

```python
from app.services.user import create_user
```

---

## 実務ポイント

```text
・循環参照に注意
・レイヤーをまたぐimportを意識
```

---

## NG

```text
View → Repository直接呼び出し
```

---

# ファイル分割

## 指針

```text
・1ファイル1責務
・巨大ファイルを作らない
```

---

## 目安

* 300行超えたら分割検討

---

# テスト（pytest）

## 基本

```python
def test_add():
    assert 1 + 2 == 3
```

---

## 例外テスト

```python
import pytest

def test_error():
    with pytest.raises(ValueError):
        func()
```

---

## 実務ポイント

```text
・Service中心にテスト
・外部依存はmock
・テストは設計の一部
```

---

# fixture

```python
import pytest

@pytest.fixture
def user():
    return {"name": "Alice"}
```

---

## ポイント

```text
テストデータの共通化
```

---

# mock

```python
from unittest.mock import patch

@patch("app.send_mail")
def test(mock_send):
    ...
```

---

## 実務ポイント

```text
・外部API
・メール
・Celery
```

は必ずmock

---

# ファイル操作

## 基本

```python
with open("file.txt") as f:
    data = f.read()
```

---

## ポイント

```text
withで自動close
```

---

# random / seed

```python
import random

random.seed(1234)
```

---

## 用途

```text
テストの再現性確保
```

---

# GC / リソース管理

## 基本

```text
通常は意識不要
```

---

## 例外

```text
・ファイル
・DB接続
・外部接続
```

---

# atexit

```python
import atexit

atexit.register(cleanup)
```

---

## 用途

```text
終了時処理
```

---

# 設計観点（重要）

---

## ① 関数は小さく

```text
1関数1責務
```

---

## ② 副作用を減らす

```text
状態変更を最小化
```

---

## ③ pure function寄り

```text
入力 → 出力
```

---

## ④ 依存を減らす

```text
テストしやすくする
```

---

# よくあるアンチパターン

* printデバッグ
* 巨大関数
* グローバル変数多用
* 例外握り潰し
* import循環
* 型ヒントなし

---

# Javaとの比較

| Java      | Python           |
| --------- | ---------------- |
| Logger    | logging          |
| Exception | raise            |
| DI        | import / fixture |
| Mockito   | mock             |
| JUnit     | pytest           |
