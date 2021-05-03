# Logging

- Spring Boot 는 내부적으로 Commons Logging을 사용하지만, 로그에 대한 구현자체는 사용자(개발자)에게 열어놓음
- 기본설정은 Java Util Logging, Log4J2, Logback을 지원
- 기본적으로 콘솔에 찍히도록 설정되어있고, 로그파일에 따로 찍히도록 설정할 수도 있음
- “Starters”를 사용하면 Logback이 로깅에 사용됨

## Log Format

```
2019-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2019-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2019-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
```

- Date and Time: Millisecond precision and easily sortable.
- Log Level: ERROR, WARN, INFO, DEBUG, or TRACE.
- Process ID.
- A --- separator to distinguish the start of actual log messages.
- Thread name: Enclosed in square brackets (may be truncated for console output).
- Logger name: This is usually the source class name (often abbreviated).
- The log message. 
**Logback은 FATAL레벨이 존재하지 않음. ERROR로 대신 매핑이 되어있음**

## Console Output
- 디폴트로 ERROR, WARN, INFO 레벨의 메세지를 로깅 
- 애플리케이션을 디버그 모드로 실행시킨다면, core loggers (embedded container, Hibernate, Spring Boot)가 더 많은 정보를 로깅하도록 설정이 바뀜

### 디버그 모드 실행 방법
1. java -jar myapp.jar —debug
2. application.properties에 debug=true 설정 추가
3. IDE 디버그 모드로 실행

- trace 는 debug모드와 달리 performance analysis가 가능하다

### trace 모드 실행 방법
1. java -jar myapp.jar —trace
2. application.properties에 trace=true 설정 추가

##  Color-coded Output

```
# %clr = conversion word
%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){yellow}
```

## File Output

- 기본적으로 Spring Boot는 File Output을 지원하지 않음
- 따로 설정 해줘야함

application.properties에서
logging.file.name 혹은 logging.file.path 부분을 설정해줘야함

|logging.file.name|logging.file.path|Example| Description |
|:----------------:|:---------------:|:------:|:---------:|
| (none) | (none) | | 콘솔 로깅 | 
| specific file | (none) | my.log | 특정 로그 파일을 지정. exact location, relatve locaion 둘다 됨 |
| (none) | specific directory | /var/log | 특정 디렉토리에 spring.log 파일을 생성. exact location, relatve locaion 둘다 됨  |

- 10 MB 마다 로테이션이 돔 (갱신)
- logging.file.max-size 설정을 통해 이 사이즈를 조정 가능
- 한 번 로테이션이 돈 로그 파일은 7일간 보관됨
- logging.file.max-history 설정을 통해 기간 조정가능
- logging.file.total-size-cap 설정을 통해 전체 로그 아카이브 사이즈 설정 가능
- 로그 아카이브가 설정한 용량을 초과할 때 backup 파일들이 삭제됨
- logging.file.clean-history-on-start 설정을 통해 시작 시 모든 로그 아카이브를 비우고 애플리케이션 시작 가능

## Log levels

- application.properties 에서 logging.level 속성을 통해 logger level 설정이 가능

level 종류: TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF

```
e.g.
logging.level.root=warn
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
```

- 위 방법은 패키지 레벨에만 적용이 가능
- 클래스 레벨의 로깅은 불가능
- 만약 원한다면 [SPRING_APPLICATION_JSON](https://docs.spring.io/spring-boot/docs/2.2.7.RELEASE/reference/html/spring-boot-features.html#boot-features-external-config-application-json) 을 활용해야 함 

## Log Groups

- 연관된 로거들을 그룹 지어서 일괄적으로 처리해줄 수 있음
- application.properties을 통해 설정 가능

```
# tomcat logger 그룹 생성
logging.group.tomcat=org.apache.catalina, org.apache.coyote, org.apache.tomcat

# 그룹 정의 후 그룹 전체의 레벨을 한번에 TRACE로 변경
logging.level.tomcat=TRACE
```

- Spring Boot에 pre-defined 로깅 그룹도 있음
- e.g. web (org.springframework.core.codec, org.springframework.http, org.springframework.web…), sql (org.springframework.jdbc.core, org.hibernate.SQL, org.jooq.tools.LoggerListener)

## Custom Log Configuration

- org.springframework.boot.logging.LoggingSystem 설정을 통해 특정 로깅 시스템을 사용하도록 설정
- none 으로 설정해주면 Spring Boot 전체 로깅 설정을 해제(disable 하게)할 수 있음
- 로깅은 ApplicationContext이 생성되기 이전에 초기화 되므로 Spring @Configuration의 @PropertySources를 통해 컨트롤 할 수 없음
- 그래서 System properties설정을 통해서만 로깅을 컨트롤 할 수 있음

## Logback Extensions

- logback.xml은 너무 일찍 로드되기 때문에 logback extension을 사용 할 수 없음
- logback-spring.xml을 사용하거나 logging.config속성을 정의해야함

## Logback 설정

[예제 코드 참고](https://github.com/woowacourse/java-deploy/tree/feat/logging/src/main/resources)

- json appender 에서 encoder를 위한 dependency 추가 해줘야함
```
e.g.
implementation 'net.logstash.logback:logstash-logback-encoder:5.2'
```

REFERENCES

- https://github.com/woowacourse/java-deploy/blob/feat/logging/src/main/resources/logback.xml
- https://docs.spring.io/spring-boot/docs/2.2.7.RELEASE/reference/html/spring-boot-features.html#boot-features-logging


