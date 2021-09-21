# [Spring Boot Tips, Tricks and Techniques](https://piotrminkowski.com/2021/01/13/spring-boot-tips-tricks-and-techniques/)

## Tip **1. 테스트에서 랜덤 HTTP 포트 사용하기**

스프링 부트 테스트에서 정적 포트를 사용하면 안됩니다. 특정 테스트에 대해 `@SpringBootTest` 에서 `webEnviroment` 필드를 사용합니다. 기본값은 `DEFINED_PORT` 로 제공하는데 이 옵션 대신 `RANDOM_PORT` 를 사용하세요. 그 이후에 `@LocalServerPort` 를 사용해 테스트에 포트 번호를 삽입할 수 있습니다.

몇 가지 Spring Boot 테스트 팁부터 시작하겠습니다. Spring Boot 테스트에서 정적 포트를 사용하면 안 됩니다. 특정 테스트에 대해 이 옵션을 설정하려면 의 `webEnvironment`필드 를 사용해야 합니다 `@SpringBootTest`. 따라서 기본값 대신 값을 `DEFINED_PORT`제공하십시오 `RANDOM_PORT`. 그런 다음 `@LocalServerPort`주석 을 사용하여 테스트에 포트 번호를 삽입할 수 있습니다 .

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class AppTest {

   @LocalServerPort
   private int port;

   @Test
   void test() {
      Assertions.assertTrue(port > 0);
   }
}
```

## Tip **2. JPA 계층 테스트에 `@DataJpaTest` 를 사용하기**

일반적으로 `@SpringBootTest` 인 경우 통합테스트에 사용합니다. 통합테스트의 문제는 전체 애플리케이션 컨텍스트를 실행한다는 점입니다. 이에 따라 테스트를 실행하는 데 필요한 총 시간이 늘어납니다. JPA 컴포넌트를 테스트를 하려고 하면 `@DataJpaTest` 와 `@Respository` 빈을 사용하는것이 좋습니다. 기본적으로 SQL 쿼리를 기록하게 되는데 `showSql` 필드를 비활성화 하면 기록하지 않습니다. `@Import` 를 사용하면 `@Service` , `@Component`  도 사용할 수 있습니다.

```java
@DataJpaTest(showSql = false)
@Import(TipService.class)
public class TipsControllerTest {

    @Autowired
    private TipService tipService;

    @Test
    void testFindAll() {
        List<Tip> tips = tipService.findAll();
        Assertions.assertEquals(3, tips.size());
    }
}
```

> 애플리케이션에 여러 통합 테스트가 있는 경우 테스트 어노테이션을 변경할때 주의하세요. 변경을 하게 되면 애플리케이션 컨텍스트의 전역 상태를 수정하므로 테스트 간에 해당 컨텍스트를 재사용하지 않을수도 있습니다.

## Tip **3. 각 테스트 후 트랜잭션 롤백하기**

일반적으로 테스트 수행 이후 모든 변경사항을 롤백하는 것이 좋습니다. 특정 테스트 중 변경사항을 다른 테스트 결과에 영향을 미치지 않아야 하기 때문입니다. 그러나 이럴때 수동으로 롤백하지 않는것이 좋습니다. 

```java
 @Test
 @Order(1)
 public void testAdd() {
     Tip tip = tipRepository.save(new Tip(null, "Tip1", "Desc1"));
     Assertions.assertNotNull(tip);
     tipRepository.deleteById(tip.getId());
 }
```

이에 따라 스프링부트는 테스트 클래스에 `@Transactional` 을 붙이면 기본적으로 롤백을 해줍니다. 그러나 클라이언트 측에서만 제대로 작동하며 애플리케이션이 서버 측에서 트랜잭션을 수행하는 경우 롤백되지 않습니다.

```java
@SpringBootTest
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
@Transactional
public class TipsRepositoryTest {

    @Autowired
    private TipRepository tipRepository;

    @Test
    @Order(1)
    public void testAdd() {
        Tip tip = tipRepository.save(new Tip(null, "Tip1", "Desc1"));
        Assertions.assertNotNull(tip);
    }

    @Test
    @Order(2)
    public void testFindAll() {
        Iterable<Tip> tips = tipRepository.findAll();
        Assertions.assertEquals(0, ((List<Tip>) tips).size());
    }
}
```

> 현재는 인메모리 임베디드 데이터베이스를 사용할때의 팁이였지만 복잡한 데이터 구조가 있는 경우 테스트가 실패하면 디버깅하는 대신 커밋된 데이터를 확인해야 할 때가 있습니다. 따라서 외부 데이터베이스를 사용하고 각 테스트 후에 데이터를 커밋해야 합니다. 아울러 테스트를 시작할때 매번 정리하는 방법도 있습니다.

## Tip **4. 논리적 "OR"이 있는 다중 스프링 조건**

`@Conditional` 빈에서 여러 조건을 정의하려면 기본적으로 "AND"를 사용합니다. 아래 보이는 경우가 그런 경우 입니다.

```java
@Bean
@ConditionalOnProperty("multipleBeans.enabled")
@ConditionalOnBean({MyBean1.class, MyBean2.class})
public MyBean myBean() {
   return new MyBean();
}
```

그렇다면 OR 조건을 정의하려면 어떻게 해야 하는가? `extends` 클래스를 만들고 `AnyNestedContion` 을 상속받으면 됩니다. 그런 다음 `@Conditional` 어노테이션을 함께 사용하면 됩니다.

```java
public class MyBeansOrPropertyCondition extends AnyNestedCondition {

    public MyBeansOrPropertyCondition() {
        super(ConfigurationPhase.REGISTER_BEAN);
    }

    @ConditionalOnBean(MyBean1.class)
    static class MyBean1ExistsCondition {}

    @ConditionalOnBean(MyBean2.class)
    static class MyBean2ExistsCondition {}

    @ConditionalOnProperty("multipleBeans.enabled")
    static class MultipleBeansPropertyExists {}
}

@Bean
@Conditional(MyBeansOrPropertyCondition.class)
public MyBean myBean() {
   return new MyBean();
}
```

## Tip **5. Maven 데이터를 애플리케이션에 주입**

Maven 데이터를 애플리케이션에 주입할 수 있는 방법은 파일에 `project` 접두사를 사용하거나 `@` 구분 기호가 있는 특수 자리 표시자를 사용하면 됩니다.

- application.properties

```yaml
maven.app=@project.artifactId@:@project.version@
```

그런 다음 `@Value`을 사용하여 속성을 애플리케이션에 주입하기만 하면 됩니다.

```java
@SpringBootApplication
public class TipsApp {
   @Value("${maven.app}")
   private String name;
}
```

한편, `BuildProperties`같이 bean을 사용할 수 있습니다. `build-info.properties`에서 사용 가능한 데이터를 저장 합니다.

```java
@SpringBootApplication
public class TipsApp {

   @Autowired
   private BuildProperties buildProperties;

   @PostConstruct
   void init() {
      log.info("Maven properties: {}, {}",
	     buildProperties.getArtifact(),
	     buildProperties.getVersion());
   }
}
```

Spring Boot Maven Plugin에서 제공하는 `build-info` 를 실행해`build-info.properties` 생성할 수 있습니다.

```
$ mvn package spring-boot:build-info
```

## Tip **6. Git 데이터를 애플리케이션에 주입**

때로는 Spring Boot 애플리케이션 내부의 Git 데이터에 접근하고 싶을 수 있습니다. 그렇게 하려면 먼저 `git-commit-id-plugin`Maven 플러그인을 사용하면 됩니다. 빌드하는 동안 `git.properties`파일 을 생성 합니다.

```xml
<plugin>
   <groupId>pl.project13.maven</groupId>
   <artifactId>git-commit-id-plugin</artifactId>
   <configuration>
      <failOnNoGitDirectory>false</failOnNoGitDirectory>
   </configuration>
</plugin>
```

`git.properties`의 내용을 `GitProperites` 빈으로 주입받을 수 있습니다.

```java
@SpringBootApplication
public class TipsApp {

   @Autowired
   private GitProperties gitProperties;

   @PostConstruct
   void init() {
      log.info("Git properties: {}, {}",
	     gitProperties.getCommitId(),
	     gitProperties.getCommitTime());
   }
}
```

## Tip **7. 초기 non-production 데이터 삽입**

때때로 데모 목적으로 응용 프로그램 시작에 일부 데이터를 삽입해야 할 떄가 있습니다. 이러한 초기 데이터 세트를 사용해 개발 중에 애플리케이션을 수동으로 테스트해야 할 때가 있습니다. 이를 위해 `data.sql` 파일을 사용하면 됩니다. 일반적으로 `src/main/resources` 에 사용합니다.

```sql
insert into tip(title, description) values ('Test1', 'Desc1');
insert into tip(title, description) values ('Test2', 'Desc2');
insert into tip(title, description) values ('Test3', 'Desc3');
```

아래와 같이 특정 프로파일에서만 기능을 활성화 하는게 좋습니다.

```java
@Profile("demo")
@Component
public class ApplicationStartupListener implements
      ApplicationListener<ApplicationReadyEvent> {

   @Autowired
   private TipRepository repository;

   public void onApplicationEvent(final ApplicationReadyEvent event) {
      repository.save(new Tip("Test1", "Desc1"));
      repository.save(new Tip("Test2", "Desc2"));
      repository.save(new Tip("Test3", "Desc3"));
   }
}
```

## Tip **8. `@Value` 대신 `@configurationPropertis`**

동일한 접두사를 가진 여러 속성이 있는 경우 `@Value` 를 사용하면 안됩니다. 대신 `@Configuration` 을 사용하는게 좋습니다. 이때 생성자 주입을 사용해야 하며 Getter도 필요합니다.

```java
@ConstructorBinding
@ConfigurationProperties("app")
@AllArgsConstructor
@Getter
@ToString
public class TipsAppProperties {
    private final String name;
    private final String version;
}

@SpringBootApplication
public class TipsApp {
    @Autowired
    private TipsAppProperties properties;
}
```

## Tip **9. Spring MVC를 사용한 오류 처리**

Spring MVC 예외 처리는 서버 예외를 클라이언트에 보내지 않도록 하는 데 매우 중요합니다. 현재 예외를 처리할 때 두 가지 권장되는 접근 방식이 있습니다. 첫 번째 단계에서는 `@ControllerAdvice`및 `@ExceptionHandler`주석 과 함께 전역 오류 처리기를 사용합니다 .좋은 방법은 애플리케이션에서 발생하는 모든 비즈니스 예외를 처리하고 여기에 HTTP 코드를 할당하는 것입니다. 기본적으로 Spring MVC는 처리되지 않은 예외에 대해 HTTP 500 코드를 반환합니다.

```java
@ControllerAdvice
public class TipNotFoundHandler {
    @ResponseStatus(HttpStatus.NO_CONTENT)
    @ExceptionHandler(NoSuchElementException.class)
    public void handleNotFound() {
    }
}
```

컨트롤러 메서드 내에서 모든 예외를 로컬로 처리할 수도 있습니다. 이 경우 특정 HTTP 코드와 함께 ResponseStatusException을 throw하면 됩니다.

```java
@GetMapping("/{id}")
public Tip findById(@PathVariable("id") Long id) {
   try {
      return repository.findById(id).orElseThrow();
   } catch (NoSuchElementException e) {
      log.error("Not found", e);
      throw new ResponseStatusException(HttpStatus.NO_CONTENT);
   }
}
```

## **Tip 10. 존재하지 않는 설정 파일 무시하기**

일반적으로 구성 파일이 없으면 응용 프로그램이 시작되지 않아야 합니다. 특히 속성에 대한 기본값을 설정할 수 있습니다. Spring 애플리케이션의 기본 동작은 구성 파일이 누락된 경우 시작되지 않으므로 변경해야 합니다. `spring.config.on-not-found`속성을 `ignore`로 설정합니다.

```
$ java -jar target/spring-boot-tips.jar \
   --spring.config.additional-location=classpath:/add.properties \
   --spring.config.on-not-found=ignore
```

시작 실패를 방지하는 또 다른 편리한 솔루션이 있습니다. 아래와 같이 config 파일 위치에 `optional`키워드를 사용할 수 있습니다 .

```
$ java -jar target/spring-boot-tips.jar \
   --spring.config.additional-location=optional:classpath:/add.properties
```

## Tip **11. 다양한 수준의 구성**

`spring.config.location`속성을 사용하여 Spring 구성 파일의 기본 위치를 변경할 수 있습니다 . 속성 소스의 우선 순위는 목록에 있는 파일의 순서에 따라 결정됩니다. 이 기능을 사용하면 일반 설정에서 시작하여 가장 애플리케이션별 설정에 이르기까지 다양한 수준의 구성을 정의할 수 있습니다. 따라서 아래에 보이는 내용이 포함된 전역 구성 파일이 있다고 가정해 보겠습니다.

```yaml
property1=Global property1
property2=Global property2
```

또한 아래와 같이 애플리케이션별 구성 파일이 있습니다. 여기에는 전역 구성 파일의 속성과 이름이 같은 속성이 포함됩니다.

```yaml
property1=App specific property1
```

그리고 그 기능을 검증하는 JUnit 테스트가 있습니다.

```java
@SpringBootTest(properties = {
    "spring.config.location=classpath:/global.properties,classpath:/app.properties"
})
public class TipsAppTest {

    @Value("${property1}")
    private String property1;

    @Value("${property2}")
    private String property2;

    @Test
    void testProperties() {
        Assertions.assertEquals("App specific property1", property1);
        Assertions.assertEquals("Global property2", property2);
    }
}
```

## Tip **12. Kubernetes에 Spring Boot 배포**

Dekorate 프로젝트를 사용하면 Kubernetes YAML 매니페스트를 수동으로 만들 필요가 없습니다. 먼저 `io.dekorate:kubernetes-spring-starter`종속성 을 포함해야 합니다 . 그런 다음 `@KubernetesApplication`생성된 YAML에 새 매개변수를 추가하거나 기본값을 재정의하는 것과 같은 주석을 사용할 수 있습니다 .

```java
@SpringBootApplication
@KubernetesApplication(replicas = 2,
    envVars = {
       @Env(name = "propertyEnv", value = "Hello from env!"),
       @Env(name = "propertyFromMap", value = "property1", configmap = "sample-configmap")
    },
    expose = true,
    ports = @Port(name = "http", containerPort = 8080),
    labels = @Label(key = "version", value = "v1"))
@JvmOptions(server = true, xmx = 256, gc = GarbageCollector.SerialGC)
public class TipsApp {
    public static void main(String[] args) {
        SpringApplication.run(TipsApp.class, args);
    }
}
```

그런 다음 Maven 빌드 명령에서 `dekorate.build`및 `dekorate.deploy`매개 변수를 `true`로 설정해야 합니다  자동으로 매니페스트를 생성하고 Kubernetes에 Spring Boot 애플리케이션을 배포합니다. Kubernetes에 애플리케이션을 배포하기 위해 Skaffold를 사용하면 Dekorate와 쉽게 통합할 수 있습니다. 

```
$ mvn clean install -Ddekorate.build =true -Ddekorate.deploy=true
```

## Tip **13. 임의의 HTTP 포트 생성**

`server.port`속성을 로 설정하면 Spring Boot는 웹 애플리케이션에 임의의 자유 포트를 할당합니다

```
server.port=0
```

사용자 정의 사전 정의 범위(예: 8000-8100)에서 임의의 포트를 설정할 수 있습니다. 그러나 생성된 포트가 할당 해제된다는 보장은 없습니다.

```java
server.port=${random.int(8000,8100)}
```
