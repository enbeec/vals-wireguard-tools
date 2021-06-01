### In order to be compatible with my scripts, follow these rules:

**For Server Configs:**
- Under each "[Peer]" include the unqualified/short hostname of that Peer in a comment like this:
```
Address = 10.0.0.101

[Peer]
#myhostname
PublicKey = ...
```

**For All Configs:**
- Comments not mentioned in this document belong directly above the "[Interface]" header.
- "[Peer]" sections are assumed to be in the following order:
```
[Peer]
#hostname (only for servers running dnsmasq)
PublicKey
Endpoint (usually only for clients)
AllowedIPs
PersistentKeepalive
```
