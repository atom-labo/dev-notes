# Python基礎

Pythonの基本的な文法・概念の整理。

---

# 概要

Pythonは：

- 動的型付け
- シンプルな構文
- 高い可読性

が特徴。

---

# 型ヒント（typing）

## 基本

```python
def add(a: int, b: int) -> int:
    return a + b
````

---

## ポイント

* 実行時には強制されない
* IDE / mypy などでチェックされる
* 可読性向上が主目的

---

## Optional（None許容）

```python
def get(value: int | None) -> int:
    ...
```

---

# mutable / immutable

## immutable（変更不可）

* int
* str
* tuple

---

## mutable（変更可）

* list
* dict
* set

---

## 注意点

```python
a = [1, 2]
b = a
b.append(3)

# aも変更される
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

## ポイント

| 引数       | 内容      |
| -------- | ------- |
| *args    | 位置引数    |
| **kwargs | キーワード引数 |

---

# クラス / self

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

## 利用

```python
user = User("Alice")
print(user.name)
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

* boilerplate削減
* **init** 自動生成

---

# property

## 概要

```text
メソッドを属性のように扱う
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

## 注意点

* 重い処理は書かない
* DBアクセスは注意

---

# decorator

## 概要

```text
関数に機能を追加する仕組み
```

---

## 例

```python
def deco(func):
    def wrapper():
        print("before")
        func()
    return wrapper

@deco
def hello():
    print("hello")
```

---

## 用途

* logging
* 認証
* transaction

---

# iterator / generator

## iterator

```python
for x in [1, 2, 3]:
    ...
```

---

## generator

```python
def gen():
    yield 1
    yield 2
```

---

## 特徴

* 遅延評価
* メモリ効率良い

---

# async / await

## 概要

```text
待ち時間中に他処理を進める
```

---

## 例

```python
async def fetch():
    await something()
```

---

## ポイント

* CPU高速化ではない
* I/O待ちに有効

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

# 注意点まとめ

* mutableの扱いに注意
* 型は強制ではない
* decoratorは関数を書き換える
* asyncは万能ではない

---
