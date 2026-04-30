# SSH

## SSH鍵の確認

```bash
ls -la ~/.ssh
````

## SSH鍵作成

```bash
ssh-keygen -t ed25519 -C "dev-user@example.com"
```

## 公開鍵表示

```bash
cat ~/.ssh/id_ed25519.pub
```

## GitHub接続確認

```bash
ssh -T git@github.com
```

## 成功例

```text
Hi dev-user! You've successfully authenticated, but GitHub does not provide shell access.
```

## 注意点

* GitHubには公開鍵 `.pub` の内容を登録する
* 秘密鍵 `id_ed25519` は絶対に公開しない
* GitHubのKey typeは `Authentication Key` を使う