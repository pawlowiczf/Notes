---
title: Remote access
layout: default
---

## Telnet - protokół zdalnego dostępu bez żadnego szyfrowania
```bash
# maksymalnie 5 połączeń (line - linia  komunikacyjna, vty - virtual terminal)
line vty 0 4 
    transport input telnet # protokół dostępu: telnet 
    login local # szukaj użytkownika w lokalne DB 

username root password root # tworzy nowego użytkownika 
enable secret root # do konfiguracji routera wymagane hasło (root)
```

## SSH - protokół zdalnego dostępu z szyfrowaniem
```bash
ip domain-name bsk.agh
line vty 0 4 
    transport input ssh 
    login local

username root password root 
enable secret root 

# crypto key zeroize rsa 
crypto key generate rsa modulus 2048 general-keys
```

