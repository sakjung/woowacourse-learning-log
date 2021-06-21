# In Memory vs 외부 DB
## 내용

**리뷰어 닉의 피드백**
> 메모리에 핵심 데이터를 보관하는 서비스는 서버가 죽거나, 새로 배포하는 경우 손실될 위험이 있기 때문에, 영구적인 데이터는 DB에 저장하고, 조회해야 합니다 :)
- 원래는 Service에서 ChessGame을 가지고 있어서 서버 메모리 내에서 게임 정보를 저장하고 있는 구조였음
- 체스 말들이 움직일 때마다 바뀐 체스판 현황을 매 수를 둘 때마다 db (MySQL)에 저장하도록 구현

# Controller & RestController
## 내용
- RestController는 Controller + ResponseBody
- RestController는 Json을 통한 api통신을 할 때 주로 이용
- ResponseEntity를 사용하면 효율적으로 원하는 response를 만들어서 반환할 수 있다
- Dto를 반환하면 Spring에서 Dto의 getter를 사용해서 json으로 자동으로 변환해줌 (@ResponseBody)

# Exception Handler & ControllerAdvice
## 내용
- 내부에서 발생하는 예외를 컨트롤러 단에서 잡아서 처리해주고 싶을 때 사용
- Exception Handler는 하나의 컨트롤러 내에서 사용하는 국지적인 예외 처리
- ControllerAdvice는 모든 컨트롤러에 대해 범용적으로 적용되는 예외 처리
- 패키지 단위로 제한할 수도 있음

e.g.
  
```
@RestControllerAdvice("wooteco.subway.auth.ui")
```

https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc#sample-application

# @JdbcTest
- 기존 auto-configuration을 무시하고 JDBC test와 관련된 configuration만 적용하도록 함
- 선언된 test는 transactional함. 각 테스트가 끝나고 롤백됨
- 자동으로 embedded in-memory 데이터베이스 (h2)를 사용해서 test를 진행해줌 (@AutoConfigureTestDatabase)

https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/autoconfigure/jdbc/JdbcTest.html

# 비즈니스 요구사항 DB Query로 처리 vs application 내부에서 처리

### 상황에 따라 다르다!
e.g.
지하철 미션에서 구간에 대한 비즈니스 요구사항을 검증할 때:
구간의 경우 요구되는 로직이 복잡하고 특정 라인의 구간들만 검사하면 되는 특징이 있음
매번 db에 쿼리를 날리는 것 보다는 필요한 구간들을 db로 부터 가져와서 어플리케이션 내부에서 도메인 객체를 통해 처리해 주는 것이 더 유리

노선이나 역에 대한 비즈니스 요구사항을 검증할 때:
요구되는 로직이 비교적 단순함 (중복이름 검사, …)
구간과 같이 모든 노선이나 역을 가져와서 도메인 객체에서 처리해주는 것이 오히려 비효율적임 (
이 경우에는 db에 쿼리를 날려서 비즈니스 로직을 해결하는 편이 더 좋음

- 추가적인 성능개선을 위해서
- 1. stored procedures (일련의 쿼리를 하나의 함수처럼 사용하기 위한 쿼리의 집합)
- 2. index 생성 (indexing)을 통해 원하는 레코드를 더 빨리 가져올 수 있도록 한다
- 3. 많은 양의 레코드에 대한 조작은 temp table 활용

https://stackoverflow.com/questions/10204790/code-performance-sql-server-query-vs-c-net-web-application

# @Transactional

- @Transactional은 선언된 class, method (interface에도 가능하나 권장되지 않음) 등의 **transactional semantics**를 나타내는 metadata
- 트랜잭션 기능이 적용된 프록시 객체 생성
- 선언된 부분의 메서드가 호출될 경우, PlatformTransactionManger를 사용하여 트랜잭션을 시작. 정상 여부에 따라 Commit 혹은 Rollback
- 선언된 class 혹은 method의 data access 로직들은 하나의 트랜잭션으로 처리됨
- 트랜잭션에 대한 다양한 설정가능

e.g. 

isolation: 다른 트랜잭션으로부터 격리된 정도. e.g. 이 트랜잭션에서 다른 트랜잭션의 commit 되지 않은 write를 확인할 수 있는가 

readOnly: 트랜잭션을 읽기 전용으로 설정 (성능 최적화, 쓰기 작업 의도적으로 방지)

https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative-annotations

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html

# Entity
- entity는 보통 비즈니스 로직과 결부 되어서 얘기할 때 언급되는 개념
- entity는 어떤 system이 모델링 하고자하는 (관심있는) 비즈니스 로직들에 대한 정보를 들고있음

(e.g. Customers, Employee, Student, Music Album …)
- entity = lightweight persistence domain object
- 통상적으로 db 테이블을 의미. 테이블의 각 row는 enttiy instance라고 할 수 있다.

- entity의 persistence state는 persistence field, persistence properties 를 통해 나타내어진다.
- persistence field는 바로 필드에 접근
- persistence properties는 getter setter를 통해 접근

## value object (VO)와의 차이점
- entity는 identity를 가짐 (primary key)
- value object는 순수 값 비교를 통해서 비교

https://docs.oracle.com/javaee/6/tutorial/doc/bnbqa.html

https://stackoverflow.com/questions/2550197/whats-the-difference-between-entity-and-class

https://stackoverflow.com/questions/14161753/class-object-entity-whats-the-difference

https://stackoverflow.com/questions/8743995/what-is-difference-between-a-model-and-an-entity

# Service에서 다른 Service를 필드로 vs Dao를 필드로

## Service에서 여러개의 Service를 들고 있는 경우

장점
- 다른 서비스에서 이미 구현된 기능 (메서드)를 사용가능 (재사용성이 증가)

단점
- Service Layer간에 불필요하게 DTO를 주고받는 경우가 생길 수 있음 -> service 관리비용 증가할 수 있음
- 순환 참조 문제가 생길 가능성이 있음

보완책
- Service를 interface로 분리해서 간접 참조한다 (서비스간의 의존성을 줄인다)

## Service에서 여러개의 Dao를 들고 있는 경우

장점
- 불필요한 DTO 생성이 없음. 도메인 객체를 통해서만 비즈니스 로직 수행 가능
- 순환 참조 걱정이 없음

단점
- 코드 재사용성이 떨어질 수 있음 (e.g. 이미 다른 서비스에 구현이 되어있는 로직을 다시 구현해야하는 상황이 생길 수 있음)

# ArgumentResolver

- 클라이언트 요청에 담긴 데이터들 (Session, Header, Cookie, data source 등등) 을 활용하여 객체를 바인딩 하고 싶을 때 사용
- custom annotation 구현과 HandlerMethodArgumentResolver interface 구현 필요

e.g.
```
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface AuthenticationPrincipal {
}

public class AuthenticationPrincipalArgumentResolver implements HandlerMethodArgumentResolver {
  @Override
  public boolean supportsParameter(MethodParameter parameter) {
      return parameter.hasParameterAnnotation(AuthenticationPrincipal.class);
  }

  @Override
  public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) {
     # 내부 구현
  }
}
```

- 내부 구현: Token을 검증하고 validation 통과 시 LoginMember 객체를 바인딩해서 반환

- Contoller에서는 LoginMember객체가 필요한 메서드에 파라미터로 @AuthenticationPrincipal를 선언
- HandlerMethodArgumentResolver를 구현한 객체에서 설정해준 대로 LoginMember객체를 바로 바인딩해서 받을 수 있음

https://www.baeldung.com/spring-mvc-custom-data-binder

https://medium.com/trabe/improve-your-argument-resolvers-using-filters-4089b28e53f3

# Interceptor
- 주로 cross-cutting concerns나 handler code의 반복 (e.g. logging, spring model에서 global하게 사용되는 파라미터를 변경 해야하는 경우)을 피하기 위해 사용됨
- cross-cutting concerns (횡단 관심사): 다른 관심사에 영향을 주는 관심사. 다른 관심사와 의존성 (결합성)이 높은 관심사 (e.g. logging, authentication)
- 실제 request를 본격적으로 handling하기 이전이나 이후에 원하는 처리를 interceptor를 활용해서 해줄 수 있음

> preHandle() - called before the actual handler <br> 
> postHandle() - called after the handler, but before view generation <br>
> afterCompletion() - called after handling and view generation

- HandlerInterceptor interface를 통해 구현 가능
- Configuration에서 addInterceptor 메서드를 통해 등록해줘야함
- interceptor 적용을 원하는 경로를 커스터마이징 해줄 수 있음
  

- 구현하는 메서드에서 boolean type을 반환
> true: request should be further processed (proceed) <br> 
> false: should not (do not proceed)

적용 예시
- login된 유저만 서비스를 이용할 수 있도록 하기위해 인터셉터를 적용해봄
- interceptor의 preHandle을 통해 controller의 handler로 넘어가기 전에 token을 valdiate
- 유효한 토큰이면 true, 아니면 401 status code와 함께 false 반환

https://www.baeldung.com/spring-mvc-handlerinterceptor

# CORS (cross origin resource sharing)
- 한 출처(origin)에서 다른 출처의 자원에 접근할 수 있는 권한을 부여하는 것
- 프론트 서버에서 fetch api등을 통해 HTTP요청이 들어오면 백엔드 서버와 origin이 다르기 때문에 CORS에러가 발생한다
- 서버단에서 별도의 설정을 통해서 프론트 서버 origin에서 서버 단에 요청이 가능하도록 해줘야한다
- 본 요청 전에 preflight 요청을 보냄 (본 요청이 가능한지 찔러보기 개념)
- OPTIONS method로 요청이 감
- preflight 요청에 대한 응답을 통해 실제 요청이 가능한지 여부를 따짐

설정 방법
1. WebMvcConfigurer 구현 객체에 addCorsMappings 오버라이드 해서 구현 
   
e.g.
```
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedMethods("*")
                .allowedOriginPatterns("http://localhost:8081");
    }
}
```

2. @CrossOrigin(origins = "*", allowedHeaders = "*") annotation Controller단에 선언

https://developer.mozilla.org/ko/docs/Web/HTTP/CORS

# Test 종류
종류와 구분에 집착하기 보다는 테스트의 목적이 중요하다!

## unit test, 단위 테스트
- 독립된 테스트들로 이루어진다 (의존성이 없는 단독적인 테스트)
- 다른 객체들에 의존하고 있는 객체에 대한 단위 테스트를 실행하려면 Mock 객체를 사용

## integration test, 통합 테스트
- 구현한 로직들의 유기적인 관계를 확인하기 위한 테스트
- 즉, 독립된 단위가 서로 연결될 때 올바르게 작동하는지 확인하는 테스트

## end to end test, Acceptance test, 인수 테스트
- 클라이언트의 관점에서 요청과 응답이 제대로 이루어 지는가에 대한 테스트
- 사용자에게 어플리케이션이 인수가 가능한가? 를 테스트

# @DirtiesContext

- 테스트 실행에 의해서 application context가 변경될 수 있음을 의미하는 어노테이션
- 테스트 클래스 (클래스 전체 메서드에 적용) 혹은 테스트 메서드에 적용 가능
- 적용하면 테스트 실행 후 새로운 application context를 재 생성 (recreate)

## Class Level
- BEFORE_CLASS: Before current test class
- BEFORE_EACH_TEST_METHOD: Before each test method in the current test class
- AFTER_EACH_TEST_METHOD: After each test method in the current test class
- AFTER_CLASS: After the current test class

## Method Level
- BEFORE_METHOD: Before the current test method
- AFTER_METHOD: After the current test method

# java bean validation

- JSR 380: Java API for bean validation
- 다양한 어노테이션을 통해 객체의 변수 값을 간편하게 validate 해줄 수 있음
- user input을 DTO로 받을 때 값 유효성 검증에 사용하면 유용함
- DTO에 어노테이션 설정 후 Controller에서 받을 때 @Valid 어노테이션과 함께 DTO를 받아야함
  e.g.
```
@PostMapping("/members")
public ResponseEntity createMember(@Valid @RequestBody MemberRequest request) {
    MemberResponse member = memberService.createMember(request);
    return ResponseEntity.created(URI.create("/members/" + member.getId())).build();
}
```

예시
* @NotNull validates that the annotated property value is not null.
* @AssertTrue validates that the annotated property value is true.
* @Size validates that the annotated property value has a size between the attributes min and max; can be applied to String, Collection, Map, and array properties.
* @Min validates that the annotated property has a value no smaller than the value attribute.
* @Max validates that the annotated property has a value no larger than the value attribute.
* @Email validates that the annotated property is a valid email address.

collection의 element에도 사용가능
```
List<@NotBlank String> preferences;
```
- validation의 조건을 충족하지 못하면 MethodArgumentNotValidException 예외를 던짐

https://www.baeldung.com/javax-validation

# @Profile vs @ActiveProfiles

@Profile
- Bean 등록 시 특정 profile에만 Bean이 등록되게 하고싶을 때 사용
- @Component class에 사용하면 특정 profile이 active할 때만 bean 등록 됨
  e.g.
  DataLoader class - 초기 데이터를 넣어 주는 class
  local 환경에서만 적용 (bean 등록)하고싶을 때
```
@Component
@Profile("local")
public class DataLoader implements CommandLineRunner {
	#내부 구현
}
```

@ActiveProfiles
JavaDoc
> ActiveProfiles is a class-level annotation that is used to declare which active bean definition profiles should be used when loading an ApplicationContext **for test classes**.

- Test 상황에서 어떤 active profile을 사용할 것인지를 선언할 때 사용
  e.g. application-test.yml 파일을 토대로한 applicationContext를 테스트 때 사용하고 싶을 때
```
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@DirtiesContext(classMode = DirtiesContext.ClassMode.BEFORE_EACH_TEST_METHOD)
@ActiveProfiles("test")
public class AcceptanceTest {
}
```

https://stackoverflow.com/questions/27216388/what-happens-in-spring-if-i-use-the-activeprofiles-annotation-on-a-configuratio/27216503#27216503

https://stackoverflow.com/questions/44055969/in-spring-what-is-the-difference-between-profile-and-activeprofiles

# Swagger

- Controller들을 스캔해서 API 문서 자동화 해줌

의존성 추가 예시
```
// swagger
implementation 'io.springfox:springfox-swagger2:2.9.2'
implementation 'io.springfox:springfox-swagger-ui:2.9.2'
compile 'io.springfox:springfox-bean-validators:2.9.2' 
```

swagger에 대한 configuration 추가 예시
```
@Configuration
@EnableSwagger2
@Import(BeanValidatorPluginsConfiguration.class)
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.basePackage("wooteco.subway"))
                .paths(PathSelectors.any())
                .build();
    }
}
```
- 마지막 validators 의존성이 있어야 SwaggerConfig에서 BeanValidatorPluginsConfiguration class를 @import할 수 있음
- import 하게 되면 swagger ui 에서 models내부의 dto 설명에 validation 사항도 추가되서 정보가 들어감
- dto validation 자체는 implementation 'org.springframework.boot:spring-boot-starter-validation’ 의존성 추가로 해결

https://www.baeldung.com/swagger-2-documentation-for-spring-rest-api

# load balancer vs reverse proxy

## load balancer
A load balancer distributes incoming client requests among a group of servers, in each case returning the response from the selected server to the appropriate client.

- 다수의 서버에 부담을 분산시켜주는 것이 주 역할

- 서버의 error response에 대해 더 유연하고 섬세하게 대처가능 (application server health check)
  e.g. 여러개의 서버 중 한 서버가 down 돼었을 때, 요청들을 해당 서버를 제외한 다른 서버들로 redirect 시켜준다

- session persistence
  여러개의 서버에서 클라이언트 session에 대한 유지가 필요할 수 있음 (e.g. e-commerce)
  이 때, 특정 클라이언트의 모든 요청들은 하나의 서버에서만 처리해주도록 할 수 있음

load balancer는 여러개의 서버가 구동중일 때 필요
reverse proxy는 꼭 여러대일 필요 없음. 서버가 하나더라도 유용

reverse proxy = website’s public face

## reverse proxy
A reverse proxy accepts a request from a client, forwards it to a server that can fulfill it, and returns the server’s response to the client.

- 보안 강화
  reverse proxy 덕분에 실제 backend server가 외부로 드러나 있지 않음. 그러므로, 악성 클라이언트가 직접적으로 접근하기 힘듦.
  또한, 대부분의 reverse proxy server가 DDoS공격에 대한 방어를 지원함
  e.g. 특정 ip의 클라이언트를 거부하는 기능 (blacklisting), 각 클라이언트당 할당된 connection갯수를 제한하는 기능

- 확장성과 유연성
  클라이언트들은 reverse proxy의 ip주소만을 바라보고 요청을 보내기 때문에 backend infrastructure 변화에 유연함
  e.g. traffic 변동에 따라 유연하게 서버 증설/감축 가능

- 성능 향상 (web accleration)
  e.g.
  Compression: response를 클라이언트에게 돌려주기 전에 압축 (e.g. gzip)해서 돌려줌. 필요한 대역폭양을 줄여줘서 네트워크상에서 성능향상에 기여

SSL termination: Encryption/Decryption은 (computationally) 비쌈. backend 서버 대신 암호화 기능을 해줌으로써 backend 서버의 부담을 줄여주고 본 기능에 집중할 수 있게 해줌

Caching: backend 서버의 response를 캐싱해둘 수 있음. 다음에 동일한 요청이 들어오면 바로 caching해둔 response를 클라이언트에게 전송

https://www.nginx.com/resources/glossary/reverse-proxy-vs-load-balancer/

https://www.nginx.com/resources/glossary/reverse-proxy-server/

# TLS 설정

- HTTP는 일반 텍스트로 통신하기 때문에 통신상의 암호화가 필요. 일반 텍스트는 패킷 스니핑을 당하면 내용이 그대로 드러나기 때문에 위험
- letsencrypt를 활용해서 무료로 TLS 인증서를 사용가능
- 인증서 발급 과정 중, 유효한 URL인지 확인을 위해 DNS TXT 레코드로 발행받은 특정 값을 추가 (DNS-01 challenge)
> Let’s Encrypt가 ACME 클라이언트에 토큰을 제공 <br> 
> -> 클라이언트는 해당 토큰과 계정 키에서 파생된 TXT 레코드를 생성하고 해당 레코드를_acme-challenge. <YOUR_DOMAIN>에 넣음 <br>
> -> Let’s Encrypt가 해당 레코드에 대한 DNS 시스템을 쿼리 <br>
> -> 일치하는 것이 있으면 인증서 발급을 진행
