## Git設定確認

```bash
git config --global -l
````

## 基本設定

```bash
git config --global user.name "dev-user"
git config --global user.email "dev-user@example.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input
```

## リポジトリ初期化

```bash
git init
```

## 初回コミット

```bash
git add .
git commit -m "chore: initial commit"
```

## GitHubリモート追加

```bash
git remote add origin git@github.com:dev-user/<repository>.git
```

## 初回push

```bash
git branch -M main
git push -u origin main
```

## リモート確認

```bash
git remote -v
```