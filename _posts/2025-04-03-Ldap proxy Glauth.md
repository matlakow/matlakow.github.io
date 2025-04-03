---
layout: post
title:  "Ldap Proxy - GLAuth and using ldapsearch"
author: matlakow
categories: [ Linux, openssl, ldap ]
image: assets/images/mas.png
---
 konfigurujemy windows wedlug https://www.miniorange.com/guide-to-setup-ldaps-on-windows-server

Pobieramy cert 
openssl s_client -connect lsrv-ad-01.ad.laito.pl:636 -showcerts </dev/null 2>/dev/null | openssl x509 -outform PEM > ad_cert.crt
kopiujemy go do 

 /usr/local/share/ca-certificates/
 a potem
 update-ca-certificates

to dziala
 LDAPTLS_REQCERT=never ldapsearch -x -H ldaps://lsrv-ad-01.ad.laito.pl -D "CN=Administrator,CN=Users,DC=ad,DC=laito,DC=pl" -W -b "DC=ad,DC=laito,DC=pl" "(sAMAccountName=jkowalski)"

 glauth
 zapytanie:
 ldapsearch -H ldap://localhost:3893 -D "CN=Administrator,CN=Users,DC=ad,DC=laito,DC=pl" -W -b "DC=ad,DC=laito,DC=pl" "(sAMAccountName=jkowalski)"

 LDAPTLS_REQCERT=never ldapsearch -x -H ldaps://localhost:3894 -D "CN=Administrator,CN=Users,DC=ad,DC=laito,DC=pl" -W -b "DC=ad,DC=laito,DC=pl" "(sAMAccountName=jkowalski)"
```

```
