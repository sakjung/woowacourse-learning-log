# Controller & RestController

- RestController는 Controller + ResponseBody
- RestController는 Json을 통한 api통신을 할 때 주로 이용
- ResponseEntity를 사용하면 효율적으로 원하는 response를 만들어서 반환할 수 있다
- Dto를 반환하면 Spring에서 Dto의 getter를 사용해서 json으로 자동으로 변환해줌 (@ResponseBody)

# Exception Handler & ControllerAdvice

- 내부에서 발생하는 예외를 컨트롤러 단에서 잡아서 처리해주고 싶을 때 사용
- Exception Handler는 하나의 컨트롤러 내에서 사용하는 국지적인 예외 처리
- ControllerAdvice를 사용하면 모든 컨트롤러에 대해 범용적으로 적용되는 예외 처리 가능
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

# 비즈니스 요구사항: DB Query로 처리 vs application 내부에서 처리

- 상황에 따라 다를 수 있다
- 지나치게 비효율적이지 않은 이상은 application에서 처리해준다

e.g.
지하철 구간 관련 비즈니스 로직 vs 지하철 역 관련 비즈니스 로직

https://stackoverflow.com/questions/10204790/code-performance-sql-server-query-vs-c-net-web-application

# Entity
- entity는 보통 비즈니스 로직과 결부 되어서 얘기할 때 언급되는 개념
- entity는 어떤 서비스가 모델링 하고자하는 (관심있는) 비즈니스 로직들에 대한 정보를 들고있음

(e.g. Customers, Employee, Student, Music Album …)
- entity = lightweight persistence domain object

value object (VO)와의 차이점
- entity는 identity를 가짐 (primary key)
- id 값을 통해 동등성 비교
- value object는 순수 값 비교를 통해서 동등성 비교

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

https://www.baeldung.com/spring-mvc-custom-data-binder

https://medium.com/trabe/improve-your-argument-resolvers-using-filters-4089b28e53f3

# Interceptor
- request를 본격적으로 handling하기 이전이나 이후에 원하는 처리를 interceptor를 활용해서 해줄 수 있음
- 주로 횡단 관심사나 handler code의 반복을 피하기 위해 사용됨

> preHandle() - called before the actual handler <br> 
> postHandle() - called after the handler, but before view generation <br>
> afterCompletion() - called after handling and view generation

- HandlerInterceptor interface를 통해 구현 가능
- Configuration에서 addInterceptor 메서드를 통해 등록해줘야함
- interceptor 적용을 원하는 url 경로를 커스터마이징 해줄 수 있음


- 구현하는 메서드에서 boolean type을 반환
> true: request should be further processed (proceed) <br> 
> false: should not (do not proceed)

https://www.baeldung.com/spring-mvc-handlerinterceptor

# CORS (cross origin resource sharing)
- 한 출처(origin)에서 실행중인 어플리케이션이 다른 출처의 자원에 접근할 수 있는 권한을 부여하는 것
- 서버에서 별도의 설정을 통해서 다른 출처에서 서버로 요청이 가능하도록 허용 해줄 수 있다

CORS 요청 시나리오
> 1. Simple requests <br>
> 2. Pre-flight <br>
> 3. credentialed requests

backend Server 에서 CORS 설정하는 방법
1. WebMvcConfigurer 구현 객체에 addCorsMappings 메서드 오버라이드해서 구현 
   
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
종류와 구분에 집착하기 보다는 테스트의 목적이 중요

## unit test, 단위 테스트
- 의존성이 없는 단독적인 테스트
- 다른 객체들에 의존하고 있는 객체에 대한 단위 테스트를 실행하려면 Mock 객체를 사용

## integration test, 통합 테스트
- 테스트 대상이 의존하고 있는 대상들과의 유기적인 관계를 확인하기 위한 테스트
- 즉, 독립된 단위들이 서로 연결될 때 올바르게 작동하는지 확인하고자 하는 목적

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
- Hibernate Validator: the reference implementation of the validation API
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

collection의 element에도 사용가능
```
List<@NotBlank String> preferences;
```
- validation의 조건을 충족하지 못하면 MethodArgumentNotValidException 예외를 던짐

https://www.baeldung.com/javax-validation

# @Profile vs @ActiveProfiles

@Profile
- 다수의 profile(application.properties, application.yml) 이 존재하는 상황에서 Bean 등록 시 특정 profile에만 Bean이 등록되게 하고싶을 때 사용
- @Component 붙은 class에 사용하면 특정 profile이 active할 때만 bean 등록 됨
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
- import 하게 되면 swagger ui 에서 models 내부의 dto 설명에 validation 사항도 추가되서 정보가 들어감

https://www.baeldung.com/swagger-2-documentation-for-spring-rest-api

# load balancer vs reverse proxy

load balancer는 여러개의 서버가 구동중일 때 필요

하지만, reverse proxy는 서버가 하나더라도 유용. 꼭 여러대일 필요 없음

## load balancer
A load balancer distributes incoming client requests among a group of servers, in each case returning the response from the selected server to the appropriate client.

- 다수의 서버에 부담을 분산시켜주는 것이 주 역할
- 서버의 error response에 대해 더 유연하고 섬세하게 대처가능 (application server health check)
- session persistence (세션 유지)

## reverse proxy
A reverse proxy accepts a request from a client, forwards it to a server that can fulfill it, and returns the server’s response to the client.

- 보안 강화
- 확장성과 유연성
- 성능 향상 (web accleration)

https://www.nginx.com/resources/glossary/reverse-proxy-vs-load-balancer/

https://www.nginx.com/resources/glossary/reverse-proxy-server/

# TLS 설정

- HTTP는 일반 텍스트로 통신하기 때문에 통신상의 암호화가 필요. 일반 텍스트는 패킷 스니핑을 당하면 내용이 그대로 드러나기 때문에 위험
- letsencrypt (DNS-01 challenge)
