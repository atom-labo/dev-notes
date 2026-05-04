# Python実務

実務でよく使うPythonの機能・設計・考え方の整理。

---

# 概要

Python実務では：

- 可読性
- 再利用性
- テスト容易性

が重要になる。

---

# typing（型ヒント）

## 基本

```python
def func(x: int) -> str:
    return str(x)
````

---

## よく使う型

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

## ポイント

* 実行時には強制されない
* IDE / mypyでチェック
* 可読性向上

---

# logging

## 概要

```text
printではなく logging を使う
```

---

## 基本

```python
import logging

logger = logging.getLogger(__name__)

logger.info("message")
```

---

## レベル

| レベル      | 用途   |
| -------- | ---- |
| DEBUG    | 詳細   |
| INFO     | 通常ログ |
| WARNING  | 注意   |
| ERROR    | エラー  |
| CRITICAL | 致命的  |

---

## ポイント

* printは使わない
* 構造化ログを意識する（JSONなど）

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

## ポイント

* 例外は握り潰さない
* 必要に応じて再throw

---

## 例外の流れ

```text
raise → 上位へ伝播 → handlerで処理
```

---

# import / module

## 基本

```python
import os
from datetime import datetime
```

---

## 相対import

```python
from .models import User
```

---

## ポイント

* 循環参照に注意
* importはファイル先頭が基本
* 必要なものだけimport

---

# testing（pytest）

## 基本

```python
def test_add():
    assert 1 + 2 == 3
```

---

## 特徴

* assertそのまま使う
* シンプル

---

## 例外テスト

```python
import pytest

def test_error():
    with pytest.raises(ValueError):
        func()
```

---

## fixture

```python
import pytest

@pytest.fixture
def user():
    return User(name="Alice")
```

---

## mock

```python
from unittest.mock import patch

@patch("app.send_mail")
def test(mock_send):
    ...
```

---

## ポイント

* 小さくテスト
* 外部依存はmock
* DBを直接触らないテストも重要

---

# random / seed

## 再現性確保

```python
import random

random.seed(1234)
```

---

## 用途

* テスト
* デバッグ

---

# ファイル操作

## 基本

```python
with open("file.txt") as f:
    data = f.read()
```

---

## ポイント

* withで自動close
* 明示的close不要

---

# GC / リソース管理

## 例

```python
import gc

gc.get_objects()
```

---

## ポイント

* 通常は意識不要
* ファイルや接続は明示的に閉じる

---

# atexit

## 概要

```text
終了時処理登録
```

---

## 例

```python
import atexit

atexit.register(cleanup)
```

---

# 実務での設計ポイント

## 関数を小さくする

```text
1関数1責務
```

---

## 副作用を減らす

```text
状態変更を最小化
```

---

## pure function寄りにする

```text
入力 → 出力
```

---

# よくあるアンチパターン

* printデバッグ
* 巨大関数
* グローバル変数多用
* 例外握り潰し
* import循環

---

# Javaとの比較

| Java      | Python           |
| --------- | ---------------- |
| Logger    | logging          |
| Exception | raise            |
| DI        | fixture / import |
| Mockito   | mock             |
| JUnit     | pytest           |

---

# まとめ

* loggingは必須
* 例外は設計の一部
* pytestが基本
* 型ヒントは積極的に使う
* 小さく分割する

