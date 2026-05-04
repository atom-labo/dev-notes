# 調査・コードリーディング

画面事象から原因箇所まで最短で到達するための手順と考え方。

---

# 方針

```text
調査は「勘」ではなく「順序」
````

---

# 全体フロー（最重要）

```text
画面
↓
Chrome DevTools（Network）
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
```

---

# Step1：まずNetworkを見る

## 確認項目

* Request URL
* Method（GET / POST / PUT / PATCH / DELETE）
* Status Code
* Request Payload / Query Params
* Response Body
* Response Time

---

## ここで分かること

```text
・どのAPIが呼ばれているか
・成功しているか（200系 / 400系 / 500系）
・入力と出力が正しいか
```

---

# Step2：問題の層を切り分ける

```text
Frontend
API（Django）
DB
```

---

## 判断基準

| 状況           | 原因層          |
| ------------ | ------------ |
| APIレスポンスが正しい | Frontend     |
| APIレスポンスが異常  | Backend      |
| API自体が失敗     | Backend / DB |

---

# Step3：フロントを確認

## 見る場所

```text
src/api/
src/services/
src/features/
```

---

## 確認内容

* API URL
* Payload生成
* Responseのマッピング
* エラーハンドリング

---

# Step4：バックエンド入口（urls.py）

```text
URL → Viewの対応を確認
```

---

## 見る順

```text
config/urls.py
↓
apps/*/urls.py
```

---

# Step5：View

## 見るポイント

* 使用Serializer
* 呼び出すUsecase
* request.data / query_params
* status code

---

## 注意

```text
Viewは処理の入口であり、本体ではない
```

---

# Step6：Serializer

## 見るポイント

* 入力項目
* 必須 / 任意
* 型
* validate処理
* response構造

---

## よくある原因

```text
400系エラーはここが多い
```

---

# Step7：Usecase

## 見るポイント

* 処理の流れ
* transaction
* Domain呼び出し
* Repository呼び出し
* Celery呼び出し

---

## 位置づけ

```text
事象の中心
```

---

# Step8：Domain

## 見るポイント

* ビジネスルール
* 条件分岐
* バリデーション
* 例外

---

## 判断

```text
仕様かバグかを判断する層
```

---

# Step9：Repository / Model

## 見るポイント

* query条件
* filter
* save / update
* index利用
* N+1問題

---

## 典型問題

```text
・データが取れない
・条件ミス
・更新されない
```

---

# 事象別の優先ルート

## 表示データがおかしい

```text
Network response
↓
Frontend mapping
↓
Serializer response
↓
Repository query
```

---

## 保存できない（400）

```text
Payload
↓
Serializer validation
↓
Domain rule
```

---

## 500エラー

```text
backend log（stacktrace）
↓
Usecase
↓
例外箇所
```

---

## 403 / 権限エラー

```text
request.user
↓
permission
↓
Domain認可
```

---

## 404

```text
URL / ID
↓
Repository filter
```

---

# 調査の思考パターン

## ① 入出力を疑う

```text
入力が正しいか
出力が正しいか
```

---

## ② 「どこでズレたか」を探す

```text
Frontend → API → Usecase → DB
```

---

## ③ 最後に正しかった地点を見つける

```text
そこから下が原因
```

---

## ④ 仮説を立てる

```text
原因を予測してからコードを見る
```

---

# VSCodeで使う操作

| 操作        | 目的                          |
| --------- | --------------------------- |
| 全文検索      | URL / API名 / エラー文言          |
| 定義へ移動     | 呼び出し先を追う                    |
| 参照を検索     | 影響範囲確認                      |
| ファイル検索    | urls / usecase / serializer |
| Git blame | 変更履歴確認                      |

---

# よくある失敗

* いきなり深い層を見る
* Networkを見ない
* ログを見ない
* 仮説なしで読む
* フロントだけ / バックだけで判断

---

# コツ

## コツ①

```text
まずNetworkで事実確認
```

---

## コツ②

```text
浅い層から順に追う
```

---

## コツ③

```text
ログと突き合わせる
```

---

## コツ④

```text
「なぜ」より「どこで」を先に
```
