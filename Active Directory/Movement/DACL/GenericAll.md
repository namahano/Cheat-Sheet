## ユーザーに対するGenericAll権限

この特権は、攻撃者にターゲットユーザーアカウントに対する完全な制御を行える。`Get-ObjectAcl`コマンドを使用して`GenericAll`権限が確認されると、攻撃者は以下を行うことができる。

- [ForceChangePassword](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Movement/DACL/ForceChangePassword.md)
- [Targeted Kerberoasting](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Movement/DACL/Targeted%20Kerberoasting.md)

## グループに対するGenericAll権限

この特権により、攻撃者は `Domain Admins` のようなグループに対して `GenericAll` 権限を持っている場合、グループメンバーシップを操作することができる。`Get-NetGroup` を使用してグループの識別名を特定した後、攻撃者は以下の操作を行える

- [AddMember](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Movement/DACL/AddMember.md)

