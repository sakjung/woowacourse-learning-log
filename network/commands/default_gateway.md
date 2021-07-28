# default gateway

```
# linux only
ip route show default

default via 172.17.0.1 dev eth0 
```

```
# multiple different unix like systems (Linux, Mac, Unix)
netstat -nr

## Linux (Docker Ubuntu)
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         172.17.0.1      0.0.0.0         UG        0 0          0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 eth0

## Mac
Routing tables

Internet:
Destination        Gateway            Flags        Netif Expire
default            192.168.0.1        UGSc           en0       
127                127.0.0.1          UCS            lo0       
127.0.0.1          127.0.0.1          UH             lo0       
169.254            link#6             UCS            en0      !
169.254.149.152    c8:34:8e:1b:3e:3   UHLSW          en0      !
192.168.0          link#6             UCS            en0      !
192.168.0.1/32     link#6             UCS            en0      !
192.168.0.1        88:36:6c:63:3:98   UHLWIir        en0   1198
192.168.0.3/32     link#6             UCS            en0      !
192.168.0.102      10:5:1:4e:22:c0    UHLWI          en0    704
224.0.0/4          link#6             UmCS           en0      !
224.0.0.251        1:0:5e:0:0:fb      UHmLWI         en0       
239.255.255.250    1:0:5e:7f:ff:fa    UHmLWI         en0       
255.255.255.255/32 link#6             UCS            en0      !

Internet6:
Destination                             Gateway                         Flags         Netif Expire
default                                 fe80::%utun0                    UGcI          utun0       
default                                 fe80::%utun1                    UGcI          utun1       
::1                                     ::1                             UHL             lo0       
fe80::%lo0/64                           fe80::1%lo0                     UcI             lo0       
fe80::1%lo0                             link#1                          UHLI            lo0       
fe80::%en5/64                           link#4                          UCI             en5       
fe80::aede:48ff:fe00:1122%en5           ac:de:48:0:11:22                UHLI            lo0       
fe80::aede:48ff:fe33:4455%en5           ac:de:48:33:44:55               UHLWIi          en5       
fe80::%en0/64                           link#6                          UCI             en0       
fe80::1481:9a56:5110:f232%en0           96:3f:f2:80:5c:cf               UHLWI           en0       
fe80::1c52:7cfa:e72a:2636%en0           14:7d:da:41:3f:83               UHLI            lo0       
fe80::%awdl0/64                         link#7                          UCI           awdl0       
fe80::fce8:a0ff:fe01:934f%awdl0         fe:e8:a0:1:93:4f                UHLI            lo0       
fe80::%llw0/64                          link#8                          UCI            llw0       
fe80::fce8:a0ff:fe01:934f%llw0          fe:e8:a0:1:93:4f                UHLI            lo0       
fe80::%utun0/64                         fe80::8ae2:dfcf:7cef:e7c7%utun0 UcI           utun0       
fe80::8ae2:dfcf:7cef:e7c7%utun0         link#14                         UHLI            lo0       
fe80::%utun1/64                         fe80::4f7a:98:2ba0:7f27%utun1   UcI           utun1       
fe80::4f7a:98:2ba0:7f27%utun1           link#15                         UHLI            lo0       
ff00::/8                                ::1                             UmCI            lo0       
ff00::/8                                link#4                          UmCI            en5       
ff00::/8                                link#6                          UmCI            en0       
ff00::/8                                link#7                          UmCI          awdl0       
ff00::/8                                link#8                          UmCI           llw0       
ff00::/8                                fe80::8ae2:dfcf:7cef:e7c7%utun0 UmCI          utun0       
ff00::/8                                fe80::4f7a:98:2ba0:7f27%utun1   UmCI          utun1       
ff01::%lo0/32                           ::1                             UmCI            lo0       
ff01::%en5/32                           link#4                          UmCI            en5       
ff01::%en0/32                           link#6                          UmCI            en0       
ff01::%awdl0/32                         link#7                          UmCI          awdl0       
ff01::%llw0/32                          link#8                          UmCI           llw0       
ff01::%utun0/32                         fe80::8ae2:dfcf:7cef:e7c7%utun0 UmCI          utun0       
ff01::%utun1/32                         fe80::4f7a:98:2ba0:7f27%utun1   UmCI          utun1       
ff02::%lo0/32                           ::1                             UmCI            lo0       
ff02::%en5/32                           link#4                          UmCI            en5       
ff02::%en0/32                           link#6                          UmCI            en0       
ff02::%awdl0/32                         link#7                          UmCI          awdl0       
ff02::%llw0/32                          link#8                          UmCI           llw0       
ff02::%utun0/32                         fe80::8ae2:dfcf:7cef:e7c7%utun0 UmCI          utun0       
ff02::%utun1/32                         fe80::4f7a:98:2ba0:7f27%utun1   UmCI          utun1  
```
