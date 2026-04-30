# Python

WSL + pyenv + venv を前提としたPython環境のメモ。

---

## 概要

- Python本体：pyenvで管理
- 仮想環境：venvで分離
- グローバル環境は使用しない

---

## Python バージョン確認

```bash
python --version
which python
````

期待：

```bash
Python 3.x.x
/home/dev-user/.pyenv/shims/python
```

---

## pyenv

### バージョン確認

```bash
pyenv --version
```

### インストール済み一覧

```bash
pyenv versions
```

### Python インストール

```bash
pyenv install 3.12.1
```

### 使用バージョン指定

```bash
pyenv global 3.12.1
```

---

## 仮想環境（venv）

### 作成

```bash
python -m venv .venv
```

---

### 有効化

```bash
source .venv/bin/activate
```

成功例：

```bash
(.venv) dev-user@dev-machine:~/projects/study/python$
```

---

### 無効化

```bash
deactivate
```

---

### 実行Python確認

```bash
which python
```

期待：

```bash
/home/dev-user/projects/study/python/.venv/bin/python
```

---

## pip

### バージョン確認

```bash
pip --version
```

---

### パッケージインストール

```bash
pip install requests
```

---

### インストール一覧

```bash
pip list
```

---

## requirements.txt

### 出力

```bash
pip freeze > requirements.txt
```

---

### インストール

```bash
pip install -r requirements.txt
```

---

## プロジェクト構成例

```text
python/
├─ .venv/
├─ main.py
├─ requirements.txt
└─ README.md
```

---

## 実行

```bash
python main.py
```

---

## 注意点

* pip install は必ず venv 有効時に実行
* グローバルPythonは使わない
* `.venv/` はGit管理しない

---

## .gitignore

```gitignore
.venv/
__pycache__/
*.pyc
```

---

## VS Code連携

* フォルダを開く

```bash
code .
```

* Interpreter を `.venv` に設定

---

## トラブルシュート

### python のパスがおかしい

```bash
which python
```

→ `.venv` または `pyenv` 配下になっているか確認

---

### venv が有効化されない

```bash
source .venv/bin/activate
```

---

### pip install が global に入る

→ venv 未有効の可能性

---

## ポリシー

* 1プロジェクト = 1venv
* 環境は壊して作り直す前提
* 依存は requirements.txt で管理
