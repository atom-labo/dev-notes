# VS Code

## WSL内のディレクトリを開く

WSL内で実行。

```bash
code .
````

## 正常確認

VS Code 左下に以下が表示されていればOK。

```text
WSL: Ubuntu-22.04
```

## 基本方針

* VS Code本体はWindows側に入れる
* 実行環境はWSL側を使う
* Python / Git / Node などはWSL内で管理する

## よく使う操作

### コマンドパレット

```text
Ctrl + Shift + P
```

### WSLへ接続

```text
WSL: Connect to WSL
```