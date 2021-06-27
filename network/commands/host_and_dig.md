# `host` command

- basic utility for looking up records in DNS
- amazon ec2 instance상에 띄워진 linux server의 경우, Amazon DNS server에 query를 날려서 조회
```
$ host google.com

google.com has address 142.250.196.142
google.com has IPv6 address 2404:6800:4004:81d::200e
google.com mail is handled by 30 alt2.aspmx.l.google.com.
google.com mail is handled by 40 alt3.aspmx.l.google.com.
google.com mail is handled by 50 alt4.aspmx.l.google.com.
google.com mail is handled by 10 aspmx.l.google.com.
google.com mail is handled by 20 alt1.aspmx.l.google.com.
```
```
# query for type A records
# looking up the addressses of IPv4 hosts
host -t a google.com

google.com has address 142.250.196.142
```

# `dig` command

- host 보다 더 script 형태임. 즉, DNS server의 configuration file에 저장되어 있는 상태 그대로와 더 가까운 형태
- host command 결과가 더 human readable 하다고 볼 수 있음

```
dig google.com

; <<>> DiG 9.11.3-1ubuntu1.15-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 4249
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		189	IN	A	172.217.31.174

;; Query time: 2 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Sun Jun 27 07:27:36 UTC 2021
;; MSG SIZE  rcvd: 55
```

- IN A: Address
- QUESTION SECTION: what is the request we send to the DNS system?
- ANSWER SECITON: what we got back
- 밑에 추가적인 metadata도 딸려 나오는 특징이 있음
- SERVER:  어떤 server가 응답을 줬는지
