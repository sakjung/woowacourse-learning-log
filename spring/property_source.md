# @PropertySource

- Spring의 `Environment`를 통해 `PropertySource`를 활용할 수 있음

```
@Configuration
@PropertySource("classpath:/com/myco/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
```

- `app.properties` 는 `testbean.name=MyTestBean`이라는 key-value pair를 가지고 있다
- 위 코드를 통해 `Environment`로 부터 `MyTestBean`이라는 값을 리턴받을 수 있다.
- `testBean.getName()` 은 `myTestBean`을 반환

```
@Configuration
@PropertySource("classpath:/com/${my.placeholder:default/path}/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
```

- ${…} placeholders: environment (e.g. system properties, environment variables)에 이미 등록되어있는 property sources set을 활용 할 수 있음
- my.placeholder는 상응하는 값을 리턴함
- my.placeholder를 resolve할 수 없는 경우 default/path 가 디폴트로 사용됨
- 둘다 찾을 수 없다면 `IllegalArgumentException` 반환

REFERENCE
https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-using-propertysource
