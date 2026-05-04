# Python 環境構築

WSL + pyenv + venv を前提としたPython環境のメモ。

個人学習・個人開発向けの最小構成をベースとする。

---

# 概要

- Python本体：pyenvで管理
- 仮想環境：venvで分離
- グローバル環境は使用しない
- lint / format：Ruff
- 必要に応じて uv を利用

---

# Python バージョン確認

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

# pyenv

## バージョン確認

```bash
pyenv --version
```

---

## インストール済み一覧

```bash
pyenv versions
```

---

## Python インストール

```bash
pyenv install 3.12.1
```

---

## グローバル設定

```bash
pyenv global 3.12.1
```

---

## プロジェクト単位設定（推奨）

```bash
pyenv local 3.12.1
```

実行すると `.python-version` が生成される。

プロジェクトごとにPythonバージョンを固定できる。

---

# 仮想環境（venv）

## 作成

```bash
python -m venv .venv
```

---

## 有効化

```bash
source .venv/bin/activate
```

成功例：

```bash
(.venv) dev-user@dev-machine:~/projects/study/python$
```

---

## 無効化

```bash
deactivate
```

---

## 実行Python確認

```bash
which python
```

期待：

```bash
/home/dev-user/projects/study/python/.venv/bin/python
```

---

# pip

## バージョン確認

```bash
pip --version
```

---

## パッケージインストール

```bash
pip install requests
```

---

## インストール一覧

```bash
pip list
```

---

# requirements.txt

## 出力

```bash
pip freeze > requirements.txt
```

---

## インストール

```bash
pip install -r requirements.txt
```

---

# uv

高速なPython package manager。

必要に応じて利用する。

---

## インストール

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## dependency同期

```bash
uv sync
```

---

## コマンド実行

```bash
uv run pytest
```

---

## Ruff実行

```bash
uv run ruff check .
```

---

# Ruff

Python向け lint / formatter。

---

## install

```bash
pip install ruff
```

---

## lint

```bash
ruff check .
```

---

## format

```bash
ruff format .
```

---

# プロジェクト構成例

```text
python/
├─ .venv/
├─ main.py
├─ requirements.txt
├─ pyproject.toml
└─ README.md
```

---

# 実行

```bash
python main.py
```

---

# Git管理

## .gitignore

```gitignore
.venv/
__pycache__/
*.pyc
```

---

# VS Code連携

## フォルダを開く

```bash
code .
```

---

## Interpreter設定

`.venv` を選択する。

---

# トラブルシュート

## python のパスがおかしい

```bash
which python
```

→ `.venv` または `pyenv` 配下になっているか確認

---

## venv が有効化されない

```bash
source .venv/bin/activate
```

---

## pip install が global に入る

→ venv 未有効の可能性

---

## Ruff が認識されない

```bash
which ruff
```

→ `.venv/bin/ruff` になっているか確認

---

# ポリシー

* 1プロジェクト = 1venv
* 環境は壊して作り直す前提
* dependency は固定する
* `.venv/` はGit管理しない
* まずは標準構成（pyenv + venv + pip）を理解する
* 必要に応じて uv を利用する
