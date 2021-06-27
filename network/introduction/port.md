# Port Numbers

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

- Non Root

**1024 ~ 65535** <br>
1024 밑으로는 sudo 안하면 `nc: Permission denied` 뜸
listen하는 쪽에서 에러 메세지 나옴
- Root 

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

- `nc: Address already in Use` error message 발생

https://stackoverflow.com/questions/37570647/how-netcat-can-listen-to-the-same-port-on-the-same-host-from-two-different-termi

https://stackoverflow.com/questions/1694144/can-two-applications-listen-to-the-same-port

- web server당 하나의 port 만을 쓸 수 있음
- 하지만, web server는 그 하위에 incoming connections를 handle하는 여러개의 thread나 child processes를 가질 수있음
- incoming connections 전체를 처리하기 위한 loop도 형성 가능 (?)
- 그래서 하나의 포트에 여러개의 connection이 가능한 것

- netcat -l 은 full server 가 아님
- 그저 하나의 포트로 들어오는 signle connection을 listen할 뿐임

## how programmes handle multiple connections

1. 새로운 커넥션이 들어올 때 마다 server는 process fork를 시행 (프로세스 복제)

- OS에게 process를 두개로 쪼개달라고 부탁함 (세포분열 하듯이)
- early web server에서 사용되던 방법 (구식)
- SSHD 같은 프로그램들은 아직도 사용중인 방법
- 복제 대상 프로세스는 부모 프로세스, 복제된 프로세스는 자식 프로세스

2. process나 thread pool을 만들어서 각자가 connection을 하나씩 담당하게 만드든 방법

- recent server에서 사용되는 방법 (최신)
- 새로운 프로세스를 실행시키는 것 보다 빠름
- 한번에 처리될 수 있는 request의 양에 제한이 있는 단점이 존재 (양을 초과하면 처리속도 저하)

3. single process 가 빠르게 switching 하며 request들을 처리하는 방법

- e.g. epole fucntion (Linux)