# 調査・コードリーディング

## 画面事象からバックエンドまで追う流れ

```text
画面
↓
Chrome DevTools Network
↓
フロントのAPI呼び出し
↓
API URL
↓
Django urls.py
↓
presentations/views
↓
presentations/serializers
↓
usecases
↓
domains
↓
infrastructures/repositories
↓
models / DB
````

## 最初に見るもの

* Request URL
* Method
* Status Code
* Payload
* Response
* Query Params

## 事象別の優先ルート

### 表示データがおかしい

```text
Network response
↓
FE mapping
↓
Serializer response
↓
Usecase
↓
Repository query
```

### 保存できない

```text
Network payload / status
↓
Serializer validation
↓
Usecase
↓
Domain rule
↓
Repository save
```

### 500エラー

```text
Network response
↓
backend log
↓
View
↓
Usecase
↓
例外箇所
```

### 403 / 権限エラー

```text
request.user
↓
permission
↓
View
↓
Usecase / Domainの認可判定
```

## VSCodeで使う操作

| 操作        | 目的                    |
| --------- | --------------------- |
| 全文検索      | URL / API名 / エラー文言を探す |
| 定義へ移動     | 呼び出し先を追う              |
| 参照を検索     | 影響範囲を見る               |
| Git blame | 変更経緯を見る               |

## 一言

いきなりDomainやRepositoryを見ない。
まずNetworkでAPIを特定し、URLから順に下る。
