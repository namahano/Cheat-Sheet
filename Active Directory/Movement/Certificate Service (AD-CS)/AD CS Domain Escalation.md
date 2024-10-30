# AD CS Domain Escalation

AD の証明書サービスは、LDAP クエリを通じて列挙することができ、**エンタープライズ証明機関 (CA)**とその構成に関する情報を明らかにする。これには、特別な権限を持たないドメイン認証されたすべてのユーザーがアクセスできる。AD CS 環境での列挙と脆弱性評価には、 [**Certify**](https://github.com/GhostPack/Certify)や[**Certipy**](https://github.com/ly4k/Certipy)などのツールが使用される。

## 脆弱なテンプレートの列挙と識別

### Linux

```
certipy-ad find -dc-ip <DC_IP> -u "<USERNAME>@<DOMAIN>" -p "<PASSWORD>" -ns <DNS_NAMESERVER> -vulnerable -stdout
```

### Windows

```
Certify.exe find /vulnerable
```

#### Sliver C2

```
certify find /vulnerable
```

## AD CS Vulnerable

### Certificate Templates

- ESC1
- ESC2
- ESC3
- ESC13

### Certificate Mapping

- ESC9
- ESC10
- ESC14

### Certificate Authority

- ESC6
- ESC12

### Access Controls

- ESC4
- ESC5
- ESC7

### Unsigned Endpoints

- ESC8
- ESC11
