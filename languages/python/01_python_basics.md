# Python基礎

実務でPythonを書く上で最低限押さえるべき言語仕様と考え方。

---

# 方針

```text
「書ける」より「バグらない・読みやすい」を優先
````

---

# 型ヒント（typing）

## 基本

```python
def add(a: int, b: int) -> int:
    return a + b
```

---

## 実務での位置づけ

```text
・型は実行時に強制されない
・IDE / mypyでチェック
・可読性とバグ防止が目的
```

---

## よく使うパターン

```python
def func(x: int | None) -> int:
    if x is None:
        return 0
    return x
```

---

## ポイント

* APIの入出力は基本つける
* Service層は必須レベル
* Domain層は明示した方が安全

---

# mutable / immutable

## 分類

| 種類        | 例                 |
| --------- | ----------------- |
| immutable | int / str / tuple |
| mutable   | list / dict / set |

---

## 重要な挙動

```python
a = [1, 2]
b = a
b.append(3)

# aも変更される
```

---

## 実務ポイント

```text
・list/dictは参照渡し
・意図せず変更が伝播する
```

---

## 対策

```python
b = a.copy()
```

---

# 関数

## 基本

```python
def func():
    pass
```

---

## 可変長引数

```python
def func(*args, **kwargs):
    pass
```

---

## 実務ポイント

```text
*args → 位置引数
**kwargs → 名前付き引数
```

---

## NGパターン

```text
引数が多すぎる関数
```

---

## 推奨

```text
データ構造（dict / dataclass）にまとめる
```

---

# クラス

## 基本

```python
class User:
    def __init__(self, name: str):
        self.name = name
```

---

## self

```text
インスタンス自身
```

---

## 実務ポイント

```text
・状態を持つものだけクラス化
・ロジックだけなら関数で良い
```

---

# dataclass

## 例

```python
from dataclasses import dataclass

@dataclass
class User:
    name: str
    age: int
```

---

## メリット

```text
・__init__ 自動生成
・可読性向上
```

---

## 実務での使いどころ

* DTO的用途
* Domainの軽量オブジェクト

---

# property

## 概要

```text
メソッドを属性として扱う
```

---

## 例

```python
class User:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name
```

---

## 実務ポイント

```text
・軽い処理のみ
・DBアクセスはNG
```

---

# decorator

## 概要

```text
関数に横断処理を追加
```

---

## 例

```python
def log(func):
    def wrapper(*args, **kwargs):
        print("start")
        return func(*args, **kwargs)
    return wrapper
```

---

## 実務用途

* logging
* 認証
* transaction
* retry

---

# iterator / generator

## generator

```python
def gen():
    yield 1
    yield 2
```

---

## 特徴

```text
・遅延評価
・メモリ効率良い
```

---

## 実務用途

* 大量データ処理
* ストリーム処理

---

# async / await

## 概要

```text
I/O待ち時間の効率化
```

---

## 例

```python
async def fetch():
    await external_api()
```

---

## 重要ポイント

```text
・CPUは速くならない
・待ち時間を有効活用するだけ
```

---

## よくある誤解

```text
async = 非同期処理（バックグラウンド）
```

→ ❌違う（Celeryがそれ）

---

# Noneの扱い

## NG

```python
if x:
```

---

## OK

```python
if x is None:
```

---

## 理由

```text
0 / "" / False と区別できない
```

---

# 比較演算

## Python特有

```python
if 0 < x < 10:
    ...
```

---

## 可読性が高い

---

# 例外（exception）

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
・意味のある例外を投げる
```

---

# import

## 基本

```python
import os
from datetime import datetime
```

---

## 実務ポイント

```text
・循環参照に注意
・トップにまとめる
```

---

# Javaとの比較

| Java              | Python      |
| ----------------- | ----------- |
| 型強制               | 型ヒント        |
| getter/setter     | property    |
| AOP               | decorator   |
| Stream            | generator   |
| CompletableFuture | async/await |

---

# よくある落とし穴

* mutableの参照共有
* Noneチェックミス
* 例外握り潰し
* asyncの誤用
* decoratorの副作用
