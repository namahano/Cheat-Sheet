# AD CS Domain Escalation

AD の証明書サービスは、LDAP クエリを通じて列挙することができ、**エンタープライズ証明機関 (CA)**とその構成に関する情報を明らかにする。これには、特別な権限を持たないドメイン認証されたすべてのユーザーがアクセスできる。AD CS 環境での列挙と脆弱性評価には、 [**Certify**](https://github.com/GhostPack/Certify)や[**Certipy**](https://github.com/ly4k/Certipy)などのツールが使用される。

## 脆弱なテンプレートの列挙と識別

### Linux

```
certipy-ad find -dc-ip <DC_IP> -u "<USERNAME>@<DOMAIN>" -p "<PASSWORD>" -ns <DNS_NAMESERVER> -vulnerable -stdout
```

### Windows

```
# デフォルトの低権限グループから脆弱な証明書テンプレートを見つける
Certify.exe find /vulnerable

# 現在のユーザーコンテキストが属するすべてのグループを使用して脆弱な証明書テンプレートを見つける
Certify.exe find /vulnerable /currentuser
```

### Sliver C2

```
certify find /vulnerable
```

## AD CS Vulnerable

### Certificate Templates

- [ESC1](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC1.md)
- [ESC2](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC2.md)
- [ESC3](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC3.md)
- [ESC9](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC9.md)
- [ESC10](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC10.md)
- [ESC13](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC13.md)
- [ESC14](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Templates/ESC14.md)

### Certificate Authority

- [ESC6](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Authority/ESC6.md)
- [ESC12](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Certificate%20Authority/ESC12.md)

### Access Controls

- [ESC4](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Access%20Controls/ESC4.md)
- [ESC5](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Access%20Controls/ESC5.md)
- [ESC7](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Access%20Controls/ESC7.md)

### Unsigned Endpoints

- [ESC8](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Unsigned%20Endpoints/ESC8.md)
- [ESC11](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/Unsigned%20Endpoints/ESC11.md)
