---
layout: post
title:  "Linux Ldap Proxy - GLAuth and using ldapsearch"
author: matlakow
categories: [ Linux, openssl, ldap, glauth ]
image: assets/images/Activedirectory.png
---
Installing GLAuth is simple:

* Download a precompiled binary from the [releases](https://github.com/glauth/glauth/releases) page.
* Download the [example config file](https://github.com/glauth/glauth/blob/master/v2/sample-simple.cfg).
* Start the GLAuth server, referencing the path to the desired config file with ./glauth64 -c sample-simple.cfg

This is my config, with works with Windows 2025 AD environment:

```
debug = true
[ldap]
  enabled = true
  # run on a non privileged port
  listen = "0.0.0.0:3893"
  tls = false # enable StartTLS support
  tlsCertPath = "server.crt"
  tlsKeyPath = "server.key"
[ldaps]
  enabled = true
  listen = "0.0.0.0:3894"
  cert = "/etc/glauth/server.crt"
  key = "/etc/glauth/server.key"
[tracing]
 enabled = true
[backend]
  datastore = "ldap"
  servers = ["ldaps://lsrv-ad-01.ad.matlakow.pl:636"] 
  insecure = true
[behaviors]
  IgnoreCapabilities = false
  LimitFailedBinds = true
  NumberOfFailedBinds = 3
  PeriodOfFailedBinds = 10
  BlockFailedBindsFor = 60
  PruneSourceTableEvery = 600
  PruneSourcesOlderThan = 600

```

Then we should configure LDAPS on Windows:

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
