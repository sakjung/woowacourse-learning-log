# @Value

- 외부 `properties` 값을 주입하기 위해 사용

```
@Component
public class MovieRecommender {

    private final String catalog;

    public MovieRecommender(@Value("${catalog.name}") String catalog) {
        this.catalog = catalog;
    }
}
```

```
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig { }
```

```
# application.properties
catalog.name=MovieCatalog
```

REFERENCE
https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-value-annotations
