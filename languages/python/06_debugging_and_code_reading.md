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

---

## 最初に見るもの（Network）

* Request URL
* Method（GET / POST / PUT / PATCH / DELETE）
* Status Code
* Request Payload / Query Params
* Response Body
* Response Time

---

## 調査の基本方針

### ① どこが責務かを切り分ける

```text
Frontendか？
APIか？
DBか？
```

---

### ② 入出力を確認する

```text
requestは正しいか？
responseは正しいか？
```

---

### ③ 「どこでズレたか」を特定する

```text
Frontend → API → Usecase → DB
```

---

### ④ 最後に正しかった地点を見つける

```text
そこから下が原因
```

---

## 事象別の優先ルート

### 表示データがおかしい

```text
Network response
↓
Frontend mapping
↓
Serializer response
↓
Usecase
↓
Repository query
```

---

### 保存できない（400系）

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

---

### 500エラー

```text
Network response
↓
backend log（stacktrace）
↓
View
↓
Usecase
↓
例外箇所
```

---

### 403 / 権限エラー

```text
request.user
↓
permission（DRF）
↓
View
↓
Usecase / Domainの認可判定
```

---

### データが存在しない（404）

```text
URL / path param
↓
View
↓
Repositoryのfilter条件
```

---

## フロント → バックエンドの追い方

### フロント起点

1. NetworkでAPI特定
2. URLでコード検索（axios / fetch）
3. API関数を確認
4. Payload / Responseの組み立てを見る

---

### バックエンド起点

1. urls.py
2. View
3. Serializer
4. Usecase
5. Domain
6. Repository

---

## VSCodeで使う操作

| 操作        | 目的                                     |
| --------- | -------------------------------------- |
| 全文検索      | URL / API名 / エラー文言を探す                  |
| 定義へ移動     | 呼び出し先を追う                               |
| 参照を検索     | 影響範囲を見る                                |
| ファイル検索    | `urls.py`, `*usecase.py`, `serializer` |
| Git blame | 変更経緯・意図を確認                             |

---

## 調査時のコツ

### コツ①

```text
いきなり深い層を見ない
```

---

### コツ②

```text
まずNetworkで事実確認
```

---

### コツ③

```text
ログとNetworkを紐付ける
```

---

### コツ④

```text
「なぜそうなるか」より
「どこでそうなったか」を先に見る
```

---

いきなりDomainやRepositoryを見ない。
まずNetworkでAPIを特定し、URLから順に下る。
