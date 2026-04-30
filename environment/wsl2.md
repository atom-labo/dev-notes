# WSL2

## 状態確認

Windows側 PowerShell で実行。

```powershell
wsl -l -v
```

## 起動

```powershell
wsl
```

または：

```powershell
wsl -d Ubuntu-22.04
```

## Ubuntuから抜ける

WSL内で実行。

```bsh
exit
```

## WSLを完全停止

Windows側 PowerShell で実行。

```powershell
wsl --shutdown
```
