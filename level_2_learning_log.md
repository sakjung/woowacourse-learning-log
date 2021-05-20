# Spring

## Test
종류와 구분에 집착하기 보다는 테스트의 목적이 중요하다!

### unit test, 단위 테스트
- 독립된 테스트들로 이루어진다 (의존성이 없는 단독적인 테스트)

### integration test, 통합 테스트
- 구현한 로직들의 유기적인 관계를 확인하기 위한 테스트
- 즉, 독립된 단위가 서로 연결될 때 올바르게 작동하는지 확인하는 테스트

### end to end test (Acceptance test, 인수 테스트)
- 클라이언트의 관점에서 요청과 응답이 제대로 이루어 지는가에 대한 테스트
- 사용자에게 어플리케이션이 인수가 가능한가? 를 테스트

## @DirtiesContext

- 테스트 실행에 의해서 application context가 변경될 수 있음을 의미하는 어노테이션
- 테스트 클래스 (클래스 전체 메서드에 적용) 혹은 테스트 메서드에 적용 가능
- Default: AFTER_METHOD
- 적용하면 테스트 실행 후 새로운 application context를 재 생성 (recreate)

### Class Level
- BEFORE_CLASS: Before current test class
- BEFORE_EACH_TEST_METHOD: Before each test method in the current test class
- AFTER_EACH_TEST_METHOD: After each test method in the current test class
- AFTER_CLASS: After the current test class

### Method Level
- BEFORE_METHOD: Before the current test method
- AFTER_METHOD: After the current test method

## 왜 @Transactional을 service layer에 놓아야 하는가

- @Service는 units of work와 use cases에 대해서 알고있는 layer임 (transaction에 직접적으로 연관되어 있음)
- @Controller는 MVC 로직에 있어서 transaction 개념과 연관이 있는 data persistence에 대해서 몰라야 함
- @Controller는 view layer에 의존하므로 나중에 바뀔 여지가 있음
- @Service는 일종의 고정된 계약 (비즈니스 요구사항)이므로 view단의 변경으로 인해서 @service의 코드가 변경되는 것은 옳지 않음 

https://stackoverflow.com/questions/18498115/why-use-transactional-with-service-instead-of-with-controller

https://stackoverflow.com/questions/1079114/where-does-the-transactional-annotation-belong/1079125#1079125

https://dimitrisli.wordpress.com/2012/10/03/spring-transaction-management-best-practices/

## 비즈니스 요구사항: DB Query vs in application

상황에 따라 다르다 

e.g.
지하철 미션에서 구간에 대한 비즈니스 요구사항을 검증할 때:

- 구간의 경우 요구되는 로직이 복잡하고 특정 라인의 구간들만 검사하면 되는 특징이 있음
- 매번 db에 쿼리를 날리는 것 보다는 필요한 구간들을 db로 부터 가져와서 어플리케이션 내부에서 도메인 객체를 통해 처리해 주는 것이 더 유리

노선이나 역에 대한 비즈니스 요구사항을 검증할 때:

- 요구되는 로직이 비교적 단순 + db전체 데이터를 조회해야함 (중복이름 검사, …)
- 만약 데이터 양이 굉장히 많다면, 도메인 객체에서 처리해주는 것이 오히려 비효율적임
- 이 경우에는 db에 쿼리를 날려서 비즈니스 로직을 해결하는 편이 더 좋음

- record가 수백만건이 될 정도로 많다면 query를 통해 계산로직을 처리하는 것이 더 성능이 좋음
- 추가적인 성능개선을 위해서
- 1. write stored procedures
  - SP: DB 내부에 저장된 일련의 SQL 명령문들을 하나의 함수처럼 실행하기 위한 쿼리 집합
  - SP는 최초 실행될 때 최적화된 상태로 컴파일 되고 이후 DB에 캐쉬되어 저장됨
  - 컴파일 작업을 다시 하지 않으므로 여러번 쓰일 때 성능향상이 있음
  - SQL문이 서버에 저장되므로 클라이언트는 필요한 매개변수만 전달하면 됨
- 2. index 생성을 통해 원하는 레코드를 더 빨리 가져올 수 있도록 한다
  - 참조하고자 하는 칼럼에 indexing을 해놓으면 데이터를 가져올 때, 처음부터 탐색하지않고 바로 가져올 수 있어서 성능 향상
- 3. 많은 양의 레코드에 대한 조작은 temp table 활용
  - Temporary Table: 장시간 걸리는 쿼리의 결과를 저장해놓은 임시 테이블. 성능이 좋다

https://stackoverflow.com/questions/10204790/code-performance-sql-server-query-vs-c-net-web-application

## java bean validation

다양한 어노테이션을 통해 인스턴스 변수들의 값을 간편하게 validate 해줄 수 있음

예시
* @NotNull: validates that the annotated property value is not null.
* @AssertTrue: validates that the annotated property value is true.
* @Size: validates that the annotated property value has a size between the attributes min and max; can be applied to String, Collection, Map, and array properties.
* @Min: validates that the annotated property has a value no smaller than the value attribute.
* @Max: validates that the annotated property has a value no larger than the value attribute.
* @Email: validates that the annotated property is a valid email address.

collection의 element에도 사용가능
```
List<@NotBlank String> preferences;
```

https://www.baeldung.com/javax-validation

## Custom Binding
- 요청 데이터를 원하는 방식으로 바인딩 해서 받을 수 있다
- 복잡한 객체에 대한 바인딩을 커스텀 할 수 있다
```
e.g.
@Component
public class StringToLocalDateTimeConverter
  implements Converter<String, LocalDateTime> {

    @Override
    public LocalDateTime convert(String source) {
        return LocalDateTime.parse(
          source, DateTimeFormatter.ISO_LOCAL_DATE_TIME);
    }
}

@GetMapping("/findbydate/{date}")
public GenericEntity findByDate(@PathVariable("date") LocalDateTime date) {
    return ...;
}
```

### Argument Resolver

- Session, Header, Cookie와 같이 non-direct한 방식으로 바인딩 하고자하는 데이터가 올 때 유용
- 요청 데이터를 domain object로 커스텀 바인딩 가능

e.g.
header에 토큰이 달려오는 경우 검증하고 LoginMember를 반환해야하는 경우

https://www.baeldung.com/spring-mvc-custom-data-binder

## Interceptor

- DispatcherServlet은 HandlerAdapter를 사용하여 요청 url과 method에 상응하는 HandlerMapping(controller)을 찾아서 호출
- interceptor는 HandlerAdapter가 HandlerMapping을 호출하는 그 사이에서 작동

- HandlerInterceptor interface를 구현해야함
- preHandle(), postHandle(), afterCompletion()

----
# 배포/인프라 Q&A

## 브라우저에서 google.com를 요청할 때 통신 과정이 어떻게 이루어질까요?

요청을 보내면 OSI 7 상위 Layer에서 하위 layer로 내려가며 요청 데이터에 각 layer별 정보가 하나하나 붙는다. 마지막 physical layer까지 도달한 후, 목적지 (google.com 서버)로 데이터를 전송한다. google.com에서는 클라이언트에서 송신할 때와는 반대로, 하위 layer에서 상위 layer로 올라가며 각 layer별 정보를 없애가며 데이터를 수신한다.

## DDoS 공격에는 어떻게 대응하면 좋을까요?

- 공격 대상 영역 줄이기
  
공격 받을 수 있는 대상영역을 최소화 시켜서 공격자의 옵션을 제한하고 한 곳에서 보호 기능을 구축한다.

1. 애플리케이션이나 리소스등을 통신이 일어나지 않을 것으로 예상되는 포트, 프로토콜, 애플리케이션 등에 노출하지 않는다.
2. 데이터베이스 서버와 같은 인프라의 중요한 특정 부분에 인터넷 트래픽이 직접적으로 접근하지 못하게 제한한다.
3. 방화벽이나 ACL(엑세스 제어 목록)등을 사용하여 트래픽을 제어한다.

- 규모에 대한 대비
  
대규모 DDoS 공격을 대비한 대역폭 (전송)용량과 서버 용량에 대한 고려가 필요하다.

전송 용량:
충분한 중복 인터넷 연결. 애플리케이션을 대규모 인터넷 교환망과 가까운 곳에 배치. 콘텐츠 전송 네트워크 및 스마트 DNS 확인 서비스를 통해 사용자에게 좀 더 가까운 위치에서 웹 컨텐츠를 제공.

서버 용량:
elastic한 컴퓨팅 리소스.

- 정상 및 비정상 트래픽 파악
  트래픽이 증가할 때 호스트가 감당할 수 있는 처리량만큼의 트래픽만 수용 (속도제한).
  개별 패킷 자체를 분석하여 합법적인 트래픽만 수용 (정상 트래픽과 비정상 트래픽을 구분할 수 있는 지표, 기준이 필요)

+ Bastion Server를 구축해서 서비스 서버에 접근할 수 있는 별도의 경로를 마련해 둔다면 DDoS공격에 더 효과적으로 대응할 수 있다.

https://aws.amazon.com/ko/shield/ddos-attack-protection/

## 현재 서버에서 몇개의 연결까지 가능한가요?

`ulimit -aH` 커맨드로 확인해본 결과 총 ‘1048576’개의 연결 (소켓 생성)이 가능합니다.
open files                      (-n) 1048576

https://woowabros.github.io/experience/2018/04/17/linux-maxuserprocess-openfiles.html

## 생성한 EC2의 스토리지 용량을 재부팅 없이, 늘리려면 어떻게 해야할까요?

1. AWS EC2 UI를 통해 volume을 수정한다 
   
AWS console 로그인 -> EC2 -> Elastic Block Store -> Volumes로 들어간다. 늘리고자 하는 volume을 선택한 후 Actions -> Modify Volume을 선택해서 Volume을 늘린다.

2. partition 설정을 통해 늘린다.

```
apt install cloud-guest-utils
growpart /dev/xvda 1
```

3. file system 설정을 통해 늘린다.

```
resize2fs /dev/xvda1
```

## CPU 사용률 100%는 장애를 의미하지 않아요. 그럼에도 파악하는 이유는 무엇일까요?

cpu 사용률이 100%에 가깝다는 것은 현재 컴퓨터의 capacity 보다 더 고강도 (cpu리소스를 많이 요하는) 의 일을 처리하려고 하고 있기 때문이다. 장애상황은 아니지만, 가동중인 애플리케이션 서비스 속도가 느려질 수 있기 때문에 파악해야 한다. 과도하게 cpu를 사용하는 부분을 파악해서 적절하게 처리하는 것이 좋다.

메모리가 고갈되면 어떤 상황이 발생할까요?
메모리가 고갈되면 애플리케이션 속도가 현저히 느려진다. 그 이유는 메모리 스왑 현상이 일어나기 때문이다. 컴퓨터는 메모리가 고갈되면 메모리에서 장기간 사용되지 않은 영역의 데이터에 대한 매핑을 해제하고 HDD에 대신 저장하기 시작한다. 그리고 이 데이터가 다시 필요해지면 메모리로 되돌려서 사용한다. 이를 메모리 스왑이라 한다.

## 디스크가 꽉 차면 어떤 현상이 발생할까요? 그리고 어떤 파일들부터 지우면 좋을까요?

disk i/o 속도가 느려진다. 디스크 공간 부족으로 crash가 일어날 수도 있다.

용량을 많이 차지하면서 서비스 운영에 있어서 중요성이 떨어지는 파일부터 지우는 것을 고려하는 것이 좋을 것 같다. 서비스에 영향을 가장 적게 미치면서 공간을 빨리 확보할 수 있을 것이라고 생각된다.

## Reverse Proxy를 별도로 구성함으로써 얻는 이점을 설명해주세요.

1. Load Balancing - 운영중인 서버들의 load를 분산시켜 줄 수 있다. 그래서 하나의 서버가 과도하게 부하되지 않도록 해준다.
2. Web Acceleration - 클라이언트와 서버간의 주고받는 데이터를 압축해주거나 캐싱해주는 역할을 맡으면서 성능 향상에 기여한다. SSL encryption과 같은 encryption 과정을 대신 맡아줘서 서비스 서버의 부하를 줄여줄 수 있다.
3. Security and anonymity - 클라이언트의 요청이 reverse proxy를 거치기 때문에, 클라이언트의 identity를 보호할 수 있고 보안 공격에 대한 안정성이 더 높아진다고 볼 수 있다. 또한, reverse proxy를 활용하면, 클라이언트의 LAN 구조에 관계없이 단일 record locator 혹은 URL로 다수의 서버에 접근할 수 있다.

reference
https://www.nginx.com/resources/glossary/reverse-proxy-server/

# 도커

https://www.reddit.com/r/docker/comments/84u54w/difference_between_docker_run_dockercompose_up/

## 이미지 및 컨테이너 관리법
https://www.freecodecamp.org/news/how-to-remove-images-in-docker/

## docker Useful Commands
```
# image 확인 
$ docker images

# 실행중인 컨테이너 확인
$ docker ps (running container only)
$ docker ps -a (all containers)

# mysql image run
$ docker run -d -p 13306:3306 -e MYSQL_ROOT_PASSWORD=root --name chess-db mysql

# docker mysql console로 접속하기
$ docker exec -i -t chess-db bash
$ mysql -u root -p

# /dev/xvda1 (ubuntu virtual disk) 사용률이 100%에 가까울 때
$ sudo df
e.g.
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/xvda1       8065444 7444340    604720  93% /

# clean up docker
$ docker system prune -a -f
or
$ docker system prune --all --volumes --force

# docker file usage
$ du -h --max-depth=1 /var/lib/docker

# delete all volumes
$ docker volume rm $(docker volume ls -q)
```

# linux

## linux commands

```
# mysql 접속
$ sudo /usr/bin/mysql -u root -p

# 8080 port kill
## 프로세스 pid를 찾는 명령어
$ ps -ef | grep java
$ pgrep -f java

## 찾은 프로세스 kill
$kill -2 [PID]

## 다른 방법
$ fuser 8080/tcp
$ fuser -k 8080/tcp

## 또 다른 방법
lsof -i tcp:8080

# 빌드
$ ./gradlew clean build

# jar파일을 찾아본다.
$ find ./* -name "*jar"

# 실행
$ java -jar [jar파일명] &
```

## 자바 빌드 스크립트 (by 케빈)

```
#!/bin/sh

echo "Building Chess Game Powered By Spring Boot\n"

cd jwp-chess

./gradlew clean build

./gradlew clean build -x test (테스트 없이 빌드)

echo "\n"

echo "Running Chess Game\n"

JAR=$(find ./* -name "*jar" | grep '^./build')
nohup java -jar $JAR 1> log.txt 2>&1 &
```
터미널 세션이 끊어질 경우, background로 돌던 프로세스에
hang-up signal이 발생해 죽는 경우가 있는데요.
이 경우 nohup명령어를 활용합니다.

(e.g. $  nohup java -jar [jar파일명] 1> [로그파일명] 2>&1  &)

## npm
```
npm i -g npm@latest --verbose
npm install -g npm@latest
```

## log파일 복사
```
service server to bastion server
rsync -avzh ubuntu@192.168.0.8:/home/ubuntu/logs/ ./logs/
```

## local mysql server stop
```
service mysql stop
```

## disk usage in sorted manner
```
sudo du -x / | sort -n | tail -40
du -x -h / | sort -h | tail -40 | sort -h -r
```

## port forwarding

```
# iptables로 설정
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080

# 설정 삭제
sudo iptables -t nat -L --line-numbers
sudo iptables -t nat -D PREROUTING [라인 넘버]
```

## TLS 인증서 (letsencrypt 활용)
```
docker run -it --rm --name certbot 
-v '/etc/letsencrypt:/etc/letsencrypt' \
-v '/var/lib/letsencrypt:/var/lib/letsencrypt' certbot/certbot certonly \ 
-d 'sakjung.p-e.kr' --manual --preferred-challenges \ 
dns --server https://acme-v02.api.letsencrypt.org/directory
```

### 결과
```
IMPORTANT NOTES:
- Congratulations! Your certificate and chain have been saved at:
  /etc/letsencrypt/live/sakjung.p-e.kr/fullchain.pem
  Your key file has been saved at:
  /etc/letsencrypt/live/sakjung.p-e.kr/privkey.pem
  Your certificate will expire on 2021-08-01. To obtain a new or
  tweaked version of this certificate in the future, simply run
  certbot again. To non-interactively renew *all* of your
  certificates, run "certbot renew"
- If you like Certbot, please consider supporting our work by:

  Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
  Donating to EFF:                    https://eff.org/donate-le
```

## 주의사항
pem들이 normal file이 아닌 link들임

https://stackoverflow.com/a/67124350

### Dockerfile에 Copy하지 말고 Volume설정으로 host directory를 mount하도록 만들기

```
docker run -d -p 80:80 -p 443:443 --name proxy \
-v '/etc/letsencrypt:/etc/letsencrypt' \
-v '/var/lib/letsencrypt:/var/lib/letsencrypt' nextstep/reverse-proxy
```
