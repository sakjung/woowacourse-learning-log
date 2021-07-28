# linux

```
$ ip addr show

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
valid_lft forever preferred_lft forever
inet6 ::1/128 scope host
valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
link/ether 0a:4d:54:fa:aa:96 brd ff:ff:ff:ff:ff:ff
inet 192.168.0.118/25 brd 192.168.0.127 scope global dynamic eth0
valid_lft 2029sec preferred_lft 2029sec
inet6 fe80::84d:54ff:fefa:aa96/64 scope link
valid_lft forever preferred_lft forever

# $ ifconfig | less도 가능
```

```
$ host localhost
localhost has address 127.0.0.1
localhost has IPv6 address ::1
```

# Mac

```
$ ifconfig | less

lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
        options=1203<RXCSUM,TXCSUM,TXSTATUS,SW_TIMESTAMP>
        inet 127.0.0.1 netmask 0xff000000 
        inet6 ::1 prefixlen 128 
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1 
        nd6 options=201<PERFORMNUD,DAD>
gif0: flags=8010<POINTOPOINT,MULTICAST> mtu 1280
stf0: flags=0<> mtu 1280
en5: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        ether ac:de:48:00:11:22 
        inet6 fe80::aede:48ff:fe00:1122%en5 prefixlen 64 scopeid 0x4 
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect (100baseTX <full-duplex>)
        status: active
ap1: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 36:7d:da:41:3f:83 
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect
        status: inactive
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether 14:7d:da:41:3f:83
        
        ...
```