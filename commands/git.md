# Git Commands

## Git設定確認

```bash
git config --global -l
```

---

## 基本設定

```bash
git config --global user.name "dev-user"
git config --global user.email "dev-user@example.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input
```

---

## リポジトリ初期化

```bash
git init
```

---

## リポジトリ clone

### GitHubからclone

```bash
git clone git@github.com:dev-user/<repository>.git
```

### clone後に移動

```bash
cd <repository>
```

### clone先を指定

```bash
git clone git@github.com:dev-user/<repository>.git <directory>
```

例：

```bash
git clone git@github.com:dev-user/dev-notes.git ~/projects/study/dev-notes
```

---

## 初回コミット

```bash
git add .
git commit -m "chore: initial commit"
```

---

## GitHubリモート追加

```bash
git remote add origin git@github.com:dev-user/<repository>.git
```

---

## 初回push

```bash
git branch -M main
git push -u origin main
```

---

## push

```bash
git push
```

---

## pull

```bash
git pull
```

---

## 状態確認

```bash
git status
```

---

## 差分確認

```bash
git diff
```

---

## ログ確認

```bash
git log --oneline
```

---

## ブランチ確認

```bash
git branch
```

---

## リモート確認

```bash
git remote -v
```

---

## 注意点

- GitHub連携はSSH接続を推奨
- clone は `/home/dev-user/projects` 配下推奨
- `.venv/` や `node_modules/` はGit管理しない