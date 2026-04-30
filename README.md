# dev-notes

開発環境構築・学習・トラブル対応用の個人メモ集。

---

## 📚 Categories

### 🛠 Environment
- [WSL2](environment/wsl2.md)
- [VS Code](environment/vscode.md)
- [Git / GitHub](environment/git-github.md)

### 💻 Commands
- [Linux](commands/linux.md)
- [Git](commands/git.md)
- [PowerShell](commands/powershell.md)

### 🔧 Tools
- [SSH](tools/ssh.md)

### 🐍 Languages
- [Python](languages/python.md)

### 🚨 Troubleshooting
- (今後追加)

---

## 📌 ポリシー

- Windows本体は極力汚さない
- 開発はWSLで完結
- コマンドはコピペ可能な形で記載
- 1トピック = 1ファイル
- 環境は壊して作り直す前提

---

## 🧪 Placeholder Rule

公開用のため以下はダミー値を使用する。

- user.name: dev-user
- user.email: dev-user@example.com
- hostname: dev-machine

---

## 🏗️ 環境方針

- OS：Windows + WSL2（Ubuntu）
- エディタ：VS Code（Remote - WSL）
- バージョン管理：Git（SSH接続）
- 言語管理：pyenv
- 仮想環境：venv

---

## 🚀 よく使うコマンド

### WSL 起動
```powershell
wsl
````

### WSL 状態確認

```powershell
wsl -l -v
```

### VS Code 起動（WSL）

```bash
code .
```

### Git push

```bash
git push
```

---

## 📈 今後追加予定

* Node.js
* Go
* Docker
* CI/CD
* Troubleshooting 集

---

## 📝 運用ルール

* コマンドは必ず動作確認してから記載
* エラーは troubleshooting に蓄積
* 学習単位でファイルを分割
* 内容は随時リファクタリングする

````

---

## 反映

```bash
git add README.md
git commit -m "docs: update README"
git push
````
