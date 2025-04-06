---
layout: post
title:  "Linux Ldap Proxy - GLAuth and using ldapsearch"
author: matlakow
categories: [ Linux, openssl, ldap, glauth ]
image: assets/images/Activedirectory.png
---
Installing GLAuth is simple:

* Download a precompiled binary from the** **[releases](https://github.com/glauth/glauth/releases) page.
* Download the** **[example config file](https://github.com/glauth/glauth/blob/master/v2/sample-simple.cfg).
* Start the GLAuth server, referencing the path to the desired config file with** **`-c ./glauth64 -c sample-simple.cfg`

This is my config, with works with Windows 2025 AD environment:



First we configure LDAPS on Windows:

```
https://www.miniorange.com/guide-to-setup-ldaps-on-windows-server
```

When we finish, we can check if everything is all right:

```
openssl s_client -connect lsrv-ad-01.ad.matlakow.pl:636 -showcerts
```

Then we should download cert from AD:

```
openssl s_client -connect lsrv-ad-01.ad.matlakow.pl:636 -showcerts /dev/null | openssl x509 -outform PEM > ad_cert.crt
```

Let's copy cert:

```
cp ad_cert.crt /usr/local/share/ca-certificates/
```

and:

```
update-ca-certificates
```

now we can query the domain, and then query the proxy:

domain:

```
LDAPTLS_REQCERT=never ldapsearch -x -H ldaps://lsrv-ad-01.ad.matlakow.pl -D "CN=Administrator,CN=Users,DC=ad,DC=matlakow,DC=pl" -W -b "DC=ad,DC=matlakow,DC=pl" "(sAMAccountName=jkowalski)"
```

glauth via ldap:

```
ldapsearch -H ldap://localhost:3893 -D "CN=Administrator,CN=Users,DC=ad,DC=matlakow,DC=pl" -W -b "DC=ad,DC=matlakow,DC=pl" "(sAMAccountName=jkowalski)"
```

glauth via ldaps:

```
LDAPTLS_REQCERT=never ldapsearch -x -H ldaps://localhost:3894 -D "CN=Administrator,CN=Users,DC=ad,DC=matlakow,DC=pl" -W -b "DC=ad,DC=matlakow,DC=pl" "(sAMAccountName=jkowalski)"
```

```

```
