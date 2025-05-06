---
title: GLBP
layout: default
---

# GLBP - Gateway Load Balancing Protocol 
Zapewnia redundację bramy (jak HSRP), ale także równoważy obciążenie między wieloma routerami brzegowymi. W tym protokole, wszystkie routery aktywnie przekazują ruch. 

## Podstawy działania 
Wszystkie routery mają GLBP dzielą ten sam wirtualny adres IP, ale każdy z osobna ma własny wirtualny adres MAC. Router odpowiada za przesył pakietów z podgrupy urządzeń w danej sieci. 

## Etapy działania GLBP
1. Wybranie AVG (active virtual gateway)
2. Przypisanie wielu wirtualnych adresów MAC członkom grupy. AVG - active virtual forwarder 
3. AVG odpowiada na zapytania ARP od kientów różnymi adresami MAC przypisanymi AVF-om 
4. AVG obsługują ruch kierowany do wirtualnego routera na różne adresy MAC


```bash
interface GigabitEthernet0/0
 ip address 192.168.1.2 255.255.255.0
 glbp 1 ip 192.168.1.1
 glbp 1 priority 110
 glbp 1 preempt
 glbp 1 load-balancing round-robin
```

![GLBP Configuration](glbp-conf.png)