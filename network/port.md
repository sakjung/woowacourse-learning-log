# Port numbers

일반적으로 server는 application을 위한 전용 port를 가짐

```
e.g.

http = 80
https = 443
ssh = 22
```
- client가 connection을 먼저 시작
- client는 임의의 포트를 사용중
- client가 connection 요청을 하면 OS가 traffic들을 client의 주소값과 port number를 통해 구분짓는다


## port number range

- non root

**1024 ~ 65535** <br>
1024 밑으로는 sudo 안하면 `nc: Permission denied` 뜸
listen하는 쪽에서 에러 메세지 나옴
- root 

**1 ~ 65535**

최대 port number인 65535 넘어가면 connection 하는 쪽에서 에러나옴 <br>
`nc: port number too large`

일반 user (non-root)는 1024밑으로 사용불가임 <br><br>
근데 ssh나 http는 1024 보다 낮은 port number를 사용하는데 이게 어떻게 가능한 걸까?

## port number from 0 up to 1023

- 시스템의 superuser account 나 Unix의 root에 의해서 실행되는 프로그램들을 위해서 특별 배정된 port number들임
- reserved parking spots 같은 개념
- 그래서 80 port에 web server를 run 하고자 할때 root 사용자로서 하거나 sudo command가 필요함
- 보안상의 이유로 web server는 start up한 이후 root 로서의 권한을 바로 없애버린다

## netcat (two terminals on same host listening on the same port)

https://stackoverflow.com/questions/37570647/how-netcat-can-listen-to-the-same-port-on-the-same-host-from-two-different-termi

