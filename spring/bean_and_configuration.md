# @Bean and @Configuration

## @Bean

- Bean annotation은 한 객체의 생성, 설정, 초기화를 담당하는 메서드의 관리를 Spring IoC container에게 맡김
- 어떤 Spring @Component가 달린 객체든지 @Bean이 달린 메서드를 사용 가능
- 보통 @Configuration에서 주로 사용됨
- @Bean어노테이션을 메서드에 적용하면 `ApplicationContext`에 빈에 대한 정의가 메서드의 반환 타입과 함께 등록된다
- 디폴트로 빈의 이름은 메서드 이름과 같다

```
@Configuration
public class AuthenticationPrincipalConfig {

    // AuthService 빈을 등록하기
    @Bean
    public AuthService authService() {
        return new AuthService();
    }

    // AuthenticationPrincipalArgumentResolver를 빈 등록하고 authService에 대한 의존성을 주입하기
    @Bean
    public AuthenticationPrincipalArgumentResolver authenticationPrincipalArgumentResolver(AuthService authService) {
        return new AuthenticationPrincipalArgumentResolver(authService);
    }
}
```
- 위 코드를 통해 ApplicationContext에 authService라는 이름의 빈을 등록했다
- 이 빈은 AuthService타입의 인스턴스에 귀속된 빈이다
- authenticationPrincipalArgumentResolver 빈은 AuthService에 대한 의존성을 주입받고 있다 (바로 위에서 Bean 등록이 되었기 때문에 가능)

REFERENCE
https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-basic-concepts
