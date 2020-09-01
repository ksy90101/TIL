# 2장. 스프링 부트 환경 설정

> [처음배우는 스프링 부트 2](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=168752840)
> [예제코드](https://github.com/ksy90101/book-spring-boot-start)

# 2.1. HelloWord

- Intelli J → Project → Spring Initializr → Spring Web 추가

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class StarterApplication {

    public static void main(String[] args) {
        SpringApplication.run(StarterApplication.class, args);
    }

    @GetMapping
    public String helloWorld(){
        return "HelloWorld";
    }
}
```

- @RestController
    - @Controller + @ResponseBody
    - 반환값이 `ResposneBody` 부분에 자동으로 바인딩 되며, RESTful 웹 서비스를 만들 때 주로 사용
- @GetMapping
    - HTTP Method 중에 Get 방식으로 경로를 받는 매핑
    - value를 지정하지 않는다는 자동으로 빈 값(`/`)이 들어감.
    - @GetMapping("/") == @GetMapping
- 실행화면

![start-spring-boot2-2-1](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-2-1.png?raw=true)

# 2.3. 그레이들 설치 및 빌드하기

- 메이븐의 설정 파일은 `XML`기반으로 작성되어 있어서 동적인 행위에 제약이 많아서 대안으로 그루비 기반의 그레들이 등장했다.
- 앤트(Ant)로 부터 기본적인 빌드 도구의 기능을, 메이븐으로부터 의존 라이브러리 관리 기능을 차용했다.
- 메이븐의 상속 구조가 아닌 설정 주입 방식을 사용하여 훨씬 유연하게 빌드 환경을 구성할 수 있다. (멀티 모듈 방식)
    - 상속 구조
        - 특정 설정을 소수의 모듈에서 공유하기 위해서는 부모 프로젝트를 생성하여 상속하게 해야 함
    - 설정 주입 방식
        - 필요한 설정을 해당 멀티 모듈에 주입해주기만 하면 된다.

## 2.3.1. 그레이들 매퍼

- gradlew : 리눅스 및 맥OS용 쉘 스크립트
- gradlew.bat : 윈도우용 배치 스크립트
- gradle/wrapper/gradle-wrapper.jar : Wrapper JAR
- gradle/wrapper/gradle-wrapper.properties : 그레이들 설정 정보 프로퍼티 파일(버전 정보 등)
- 스프링 이니셜라이저로 생성한 프로젝트는 그레이들을 자동으로 설치하는 이유가 gradle-wrapper.properties로 빌드 설정을 자동으로 해주기 때문이다.
- `distributionUrl`속성값에서 버전을 변경할 수 있다.

```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-6.4.1-bin.zip
```

- 맥OS나 리눅스 사용자라면 권한을 설정해야 한다.
    - chomd 755 gradlew
    - ./gradlew wrapper —gradle-version 4.8.1
- 그레이들 버전 확인
    - ./gradlew -v

## 2.3.2. 그레이들 멀티 프로젝트 구성하기

- 프로젝트가 여러개일 때 컬럼 하나가 추가될 때 모든 프로젝트에 모두 추가해줘야 하지만, 멀티 프로젝트를 구성하면 이러한 중복 코드를 제거할 수 있다.
- `settings.gradle` : 그레이들 설정 파일

    ```groovy
    rootProject.name = 'starter'
    ```

- 멀티 프로젝트 만드는 법
    - New → Module → gradle → Java
- 자바 프로젝트 기본 패키지 구조
    - src/main/java : 프로덕션 자바 소스 코드
    - src/test/java : 테스트 자바 소스 코드
    - src/main/resources/static : 정적 파일
    - src/main/resources/templates : 서버 사이드 템플릿 파일 경로
- 멀티 모듈을 만들 경우 settings.gradle에 `include`가 추가된다.

```groovy
rootProject.name = 'book'
include 'web'
include 'domain'
```

- 멀티 모듈를 구성하면 코드 재사용성이 높아지고 한 서비스에 관련된 여러 프로젝트를 마치 하나의 통합 프로젝트처럼 관리할 수 있다.
- 멀티 모듈에서 공통적으로 사용하는 의존성을 설정하기 위해 루트 build.gradle에 아래와 같이 설정해야 한다.

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.1.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'org.asciidoctor.convert' version '1.5.8'
    id 'java'
}

subprojects {
    group = 'starter.spring.boot'
    version = '0.0.1-SNAPSHOT'

    apply plugin: 'java'
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'

    sourceCompatibility = '1.8'

    repositories {
        mavenCentral()
    }

    dependencies {
        compileOnly 'org.projectlombok:lombok'
        annotationProcessor 'org.projectlombok:lombok'
        testImplementation('org.springframework.boot:spring-boot-starter-test') {
            exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
        }
    }

    test {
        useJUnitPlatform()
    }

    configurations {
        compileOnly {
            extendsFrom annotationProcessor
        }
    }
}
```

# 2.4. 환경 프로퍼티 파일 설정하기

[@Value와 @ConfigurationProperties를 이용해 yaml 파일 매핑하기](https://github.com/ksy90101/TIL/blob/master/spring/value-configuration-properties-ymal-mapping.md)

# 2.5. 자동 환경 설정 이해하기

- 자동 설정은 `@EnableAutoConfiguration` 또는 `@SpringBootApplication`를 사용하면 됩니다.
- @EnableAutoConfiguration을 사용할 경우 @Configuration과 함께 사용해야 한다.

### 2.5.1. 자동 환경 설정 어노테이션

- @SpringBootApplication

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

	@AliasFor(annotation = EnableAutoConfiguration.class)
	Class<?>[] exclude() default {};

	@AliasFor(annotation = EnableAutoConfiguration.class)
	String[] excludeName() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
	String[] scanBasePackages() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
	Class<?>[] scanBasePackageClasses() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
	Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

	@AliasFor(annotation = Configuration.class)
	boolean proxyBeanMethods() default true;

}
```

- @SpringBootConfiguration : 스프링부트 설정을 나타내는 어노테이션으로 @Configuration을 대체하며 스프링 부트 전용으로 사용. 예를 들어 스프링 부트의 @SpringBootTest을 사용할 때 찾기 알고리즘을 사용하여 계속 @SpringBootConfiguration을 찾기 때문에 스프링 부트에서는 필수 어노테이션 중 하나이다.
- @EnableAutoConfiguration : 자동 설정의 핵심. 클래스 경로에 지정된 내용을 기반으로 영리하게 설정 자동화를 수행. 특별한 설정값을 추가하지 않으면 기본값으로 작동
- @ComponentScan : 특정 패키지 경로를 기반으로 @Configuration에서 사용할 @Component 설정 클래스를 찾는다. @ComponentScandml basePackages 프로퍼티값에 별도의 경로를 설정하지 않으면 @ComponentScan이 위치한 패키지가 루트 경로로 설정됨.

- @SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan

### 2.5.2. @EnableAutoConfiguration 살펴보기

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

	Class<?>[] exclude() default {};

	String[] excludeName() default {};

}
```

- 여기서 자동 설정을 해주는 부분은 `@Import(AutoConfigurationImportSelector.class)`여기 입니다.

- AutoConfigurationImportSelector

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {

	private static final AutoConfigurationEntry EMPTY_ENTRY = new AutoConfigurationEntry();

	private static final String[] NO_IMPORTS = {};

	private static final Log logger = LogFactory.getLog(AutoConfigurationImportSelector.class);

	private static final String PROPERTY_NAME_AUTOCONFIGURE_EXCLUDE = "spring.autoconfigure.exclude";

	private ConfigurableListableBeanFactory beanFactory;

	private Environment environment;

	private ClassLoader beanClassLoader;

	private ResourceLoader resourceLoader;

	private ConfigurationClassFilter configurationClassFilter;

	@Override
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return NO_IMPORTS;
		}
		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
	}

	@Override
	public Predicate<String> getExclusionFilter() {
		return this::shouldExclude;
	}

	private boolean shouldExclude(String configurationClassName) {
		return getConfigurationClassFilter().filter(Collections.singletonList(configurationClassName)).isEmpty();
	}

	protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
		configurations = removeDuplicates(configurations);
		Set<String> exclusions = getExclusions(annotationMetadata, attributes);
		checkExcludedClasses(configurations, exclusions);
		configurations.removeAll(exclusions);
		configurations = getConfigurationClassFilter().filter(configurations);
		fireAutoConfigurationImportEvents(configurations, exclusions);
		return new AutoConfigurationEntry(configurations, exclusions);
	}

	@Override
	public Class<? extends Group> getImportGroup() {
		return AutoConfigurationGroup.class;
	}

	protected boolean isEnabled(AnnotationMetadata metadata) {
		if (getClass() == AutoConfigurationImportSelector.class) {
			return getEnvironment().getProperty(EnableAutoConfiguration.ENABLED_OVERRIDE_PROPERTY, Boolean.class, true);
		}
		return true;
	}

	protected AnnotationAttributes getAttributes(AnnotationMetadata metadata) {
		String name = getAnnotationClass().getName();
		AnnotationAttributes attributes = AnnotationAttributes.fromMap(metadata.getAnnotationAttributes(name, true));
		Assert.notNull(attributes, () -> "No auto-configuration attributes found. Is " + metadata.getClassName()
				+ " annotated with " + ClassUtils.getShortName(name) + "?");
		return attributes;
	}

	protected Class<?> getAnnotationClass() {
		return EnableAutoConfiguration.class;
	}

	protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}

	protected Class<?> getSpringFactoriesLoaderFactoryClass() {
		return EnableAutoConfiguration.class;
	}

	private void checkExcludedClasses(List<String> configurations, Set<String> exclusions) {
		List<String> invalidExcludes = new ArrayList<>(exclusions.size());
		for (String exclusion : exclusions) {
			if (ClassUtils.isPresent(exclusion, getClass().getClassLoader()) && !configurations.contains(exclusion)) {
				invalidExcludes.add(exclusion);
			}
		}
		if (!invalidExcludes.isEmpty()) {
			handleInvalidExcludes(invalidExcludes);
		}
	}

	protected void handleInvalidExcludes(List<String> invalidExcludes) {
		StringBuilder message = new StringBuilder();
		for (String exclude : invalidExcludes) {
			message.append("\t- ").append(exclude).append(String.format("%n"));
		}
		throw new IllegalStateException(String.format(
				"The following classes could not be excluded because they are not auto-configuration classes:%n%s",
				message));
	}

	protected Set<String> getExclusions(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		Set<String> excluded = new LinkedHashSet<>();
		excluded.addAll(asList(attributes, "exclude"));
		excluded.addAll(Arrays.asList(attributes.getStringArray("excludeName")));
		excluded.addAll(getExcludeAutoConfigurationsProperty());
		return excluded;
	}

	protected List<String> getExcludeAutoConfigurationsProperty() {
		Environment environment = getEnvironment();
		if (environment == null) {
			return Collections.emptyList();
		}
		if (environment instanceof ConfigurableEnvironment) {
			Binder binder = Binder.get(environment);
			return binder.bind(PROPERTY_NAME_AUTOCONFIGURE_EXCLUDE, String[].class).map(Arrays::asList)
					.orElse(Collections.emptyList());
		}
		String[] excludes = environment.getProperty(PROPERTY_NAME_AUTOCONFIGURE_EXCLUDE, String[].class);
		return (excludes != null) ? Arrays.asList(excludes) : Collections.emptyList();
	}

	protected List<AutoConfigurationImportFilter> getAutoConfigurationImportFilters() {
		return SpringFactoriesLoader.loadFactories(AutoConfigurationImportFilter.class, this.beanClassLoader);
	}

	private ConfigurationClassFilter getConfigurationClassFilter() {
		if (this.configurationClassFilter == null) {
			List<AutoConfigurationImportFilter> filters = getAutoConfigurationImportFilters();
			for (AutoConfigurationImportFilter filter : filters) {
				invokeAwareMethods(filter);
			}
			this.configurationClassFilter = new ConfigurationClassFilter(this.beanClassLoader, filters);
		}
		return this.configurationClassFilter;
	}

	protected final <T> List<T> removeDuplicates(List<T> list) {
		return new ArrayList<>(new LinkedHashSet<>(list));
	}

	protected final List<String> asList(AnnotationAttributes attributes, String name) {
		String[] value = attributes.getStringArray(name);
		return Arrays.asList(value);
	}

	private void fireAutoConfigurationImportEvents(List<String> configurations, Set<String> exclusions) {
		List<AutoConfigurationImportListener> listeners = getAutoConfigurationImportListeners();
		if (!listeners.isEmpty()) {
			AutoConfigurationImportEvent event = new AutoConfigurationImportEvent(this, configurations, exclusions);
			for (AutoConfigurationImportListener listener : listeners) {
				invokeAwareMethods(listener);
				listener.onAutoConfigurationImportEvent(event);
			}
		}
	}

	protected List<AutoConfigurationImportListener> getAutoConfigurationImportListeners() {
		return SpringFactoriesLoader.loadFactories(AutoConfigurationImportListener.class, this.beanClassLoader);
	}

	private void invokeAwareMethods(Object instance) {
		if (instance instanceof Aware) {
			if (instance instanceof BeanClassLoaderAware) {
				((BeanClassLoaderAware) instance).setBeanClassLoader(this.beanClassLoader);
			}
			if (instance instanceof BeanFactoryAware) {
				((BeanFactoryAware) instance).setBeanFactory(this.beanFactory);
			}
			if (instance instanceof EnvironmentAware) {
				((EnvironmentAware) instance).setEnvironment(this.environment);
			}
			if (instance instanceof ResourceLoaderAware) {
				((ResourceLoaderAware) instance).setResourceLoader(this.resourceLoader);
			}
		}
	}

	@Override
	public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
		Assert.isInstanceOf(ConfigurableListableBeanFactory.class, beanFactory);
		this.beanFactory = (ConfigurableListableBeanFactory) beanFactory;
	}

	protected final ConfigurableListableBeanFactory getBeanFactory() {
		return this.beanFactory;
	}

	@Override
	public void setBeanClassLoader(ClassLoader classLoader) {
		this.beanClassLoader = classLoader;
	}

	protected ClassLoader getBeanClassLoader() {
		return this.beanClassLoader;
	}

	@Override
	public void setEnvironment(Environment environment) {
		this.environment = environment;
	}

	protected final Environment getEnvironment() {
		return this.environment;
	}

	@Override
	public void setResourceLoader(ResourceLoader resourceLoader) {
		this.resourceLoader = resourceLoader;
	}

	protected final ResourceLoader getResourceLoader() {
		return this.resourceLoader;
	}

	@Override
	public int getOrder() {
		return Ordered.LOWEST_PRECEDENCE - 1;
	}

	private static class ConfigurationClassFilter {

		private final AutoConfigurationMetadata autoConfigurationMetadata;

		private final List<AutoConfigurationImportFilter> filters;

		ConfigurationClassFilter(ClassLoader classLoader, List<AutoConfigurationImportFilter> filters) {
			this.autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(classLoader);
			this.filters = filters;
		}

		List<String> filter(List<String> configurations) {
			long startTime = System.nanoTime();
			String[] candidates = StringUtils.toStringArray(configurations);
			boolean skipped = false;
			for (AutoConfigurationImportFilter filter : this.filters) {
				boolean[] match = filter.match(candidates, this.autoConfigurationMetadata);
				for (int i = 0; i < match.length; i++) {
					if (!match[i]) {
						candidates[i] = null;
						skipped = true;
					}
				}
			}
			if (!skipped) {
				return configurations;
			}
			List<String> result = new ArrayList<>(candidates.length);
			for (String candidate : candidates) {
				if (candidate != null) {
					result.add(candidate);
				}
			}
			if (logger.isTraceEnabled()) {
				int numberFiltered = configurations.size() - result.size();
				logger.trace("Filtered " + numberFiltered + " auto configuration class in "
						+ TimeUnit.NANOSECONDS.toMillis(System.nanoTime() - startTime) + " ms");
			}
			return result;
		}

	}

	private static class AutoConfigurationGroup
			implements DeferredImportSelector.Group, BeanClassLoaderAware, BeanFactoryAware, ResourceLoaderAware {

		private final Map<String, AnnotationMetadata> entries = new LinkedHashMap<>();

		private final List<AutoConfigurationEntry> autoConfigurationEntries = new ArrayList<>();

		private ClassLoader beanClassLoader;

		private BeanFactory beanFactory;

		private ResourceLoader resourceLoader;

		private AutoConfigurationMetadata autoConfigurationMetadata;

		@Override
		public void setBeanClassLoader(ClassLoader classLoader) {
			this.beanClassLoader = classLoader;
		}

		@Override
		public void setBeanFactory(BeanFactory beanFactory) {
			this.beanFactory = beanFactory;
		}

		@Override
		public void setResourceLoader(ResourceLoader resourceLoader) {
			this.resourceLoader = resourceLoader;
		}

		@Override
		public void process(AnnotationMetadata annotationMetadata, DeferredImportSelector deferredImportSelector) {
			Assert.state(deferredImportSelector instanceof AutoConfigurationImportSelector,
					() -> String.format("Only %s implementations are supported, got %s",
							AutoConfigurationImportSelector.class.getSimpleName(),
							deferredImportSelector.getClass().getName()));
			AutoConfigurationEntry autoConfigurationEntry = ((AutoConfigurationImportSelector) deferredImportSelector)
					.getAutoConfigurationEntry(annotationMetadata);
			this.autoConfigurationEntries.add(autoConfigurationEntry);
			for (String importClassName : autoConfigurationEntry.getConfigurations()) {
				this.entries.putIfAbsent(importClassName, annotationMetadata);
			}
		}

		@Override
		public Iterable<Entry> selectImports() {
			if (this.autoConfigurationEntries.isEmpty()) {
				return Collections.emptyList();
			}
			Set<String> allExclusions = this.autoConfigurationEntries.stream()
					.map(AutoConfigurationEntry::getExclusions).flatMap(Collection::stream).collect(Collectors.toSet());
			Set<String> processedConfigurations = this.autoConfigurationEntries.stream()
					.map(AutoConfigurationEntry::getConfigurations).flatMap(Collection::stream)
					.collect(Collectors.toCollection(LinkedHashSet::new));
			processedConfigurations.removeAll(allExclusions);

			return sortAutoConfigurations(processedConfigurations, getAutoConfigurationMetadata()).stream()
					.map((importClassName) -> new Entry(this.entries.get(importClassName), importClassName))
					.collect(Collectors.toList());
		}

		private AutoConfigurationMetadata getAutoConfigurationMetadata() {
			if (this.autoConfigurationMetadata == null) {
				this.autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
			}
			return this.autoConfigurationMetadata;
		}

		private List<String> sortAutoConfigurations(Set<String> configurations,
				AutoConfigurationMetadata autoConfigurationMetadata) {
			return new AutoConfigurationSorter(getMetadataReaderFactory(), autoConfigurationMetadata)
					.getInPriorityOrder(configurations);
		}

		private MetadataReaderFactory getMetadataReaderFactory() {
			try {
				return this.beanFactory.getBean(SharedMetadataReaderFactoryContextInitializer.BEAN_NAME,
						MetadataReaderFactory.class);
			}
			catch (NoSuchBeanDefinitionException ex) {
				return new CachingMetadataReaderFactory(this.resourceLoader);
			}
		}

	}

	protected static class AutoConfigurationEntry {

		private final List<String> configurations;

		private final Set<String> exclusions;

		private AutoConfigurationEntry() {
			this.configurations = Collections.emptyList();
			this.exclusions = Collections.emptySet();
		}

		AutoConfigurationEntry(Collection<String> configurations, Collection<String> exclusions) {
			this.configurations = new ArrayList<>(configurations);
			this.exclusions = new HashSet<>(exclusions);
		}

		public List<String> getConfigurations() {
			return this.configurations;
		}

		public Set<String> getExclusions() {
			return this.exclusions;
		}

	}

}
```

- selectImports() : 자동 설정할 빈을 결정
- getCandidateConfigurations() : 모든 후보 빈을 불러옴
    - 즉, META-INF/spring.factories에 정의된 자동 설정할 클래스들을 먼저 불러옴
- 이때 스프링 부트 스타터를 여러개 등록하면 중복된 빈들이 설정되어 있을 텐데 getExclusions() - 제외할 설정과 removeDuplicates() - 중복된 설정을 제외시켜준다.
- 마지막으로 이 중에서 프로젝트에서 사용하는 빈만 임포트할 자동 설정 대상으로 선택됨.

- 빈 등록과 자동 설정에 필요한 파일
    - META-INF/spring.factories : 자동 설정 타깃 클래스 목록으로 이곳에 선언되어 있는 클래스들이 `@EnableAutoConfiguration` 사용 시 자동 설정 타깃이 됨.
    - META-INF/spring-configuration-metadata.json : 자동 설정에 사용할 프로퍼티 정의 파일로 미리 구현되어 있는 자동 설정에 프로퍼티만 주입시켜주면 되므로 별도 환경 설정은 필요 없다.
    - org/springframework/boot/autoconfigure : 미리 구현해놓은 자동 설정 리스트로 이름은 '{특정 설정의 이름}AutoConfiguration' 형식으로 지정되어 있으며 모두 자바 설정 방식을 따르고 있다.

- 위 파일 모두 spring-boot-autoconfiguration에 미리 정의되어 있으며 지정된 프로퍼티값을 사용하여 설정 클래스 내부의 값들을 변경할 수 있음.

- 예를들어 H2를 자동 설정한다고 한다.
    - spring.factories에서 자동 설정 대상에 해당 되는지 확인
    - spring-configuration-metadata.json에서 주요 프로퍼티값들은 무엇이고 어떤 타입으로 설정할 수 있는지도 확인

    ```json
    {
          "name": "spring.h2.console.path",
          "type": "java.lang.String",
          "description": "Path at which the console is available.",
          "sourceType": "org.springframework.boot.autoconfigure.h2.H2ConsoleProperties",
          "defaultValue": "\/h2-console"
        },
    ```

    - H2의 기본 경로 값은 /h2-console인걸 확인할 수 있으며, String형인 것을 확인할 수 있다.
    - 다른 경로로 변경하기 위해 속성값에 추가한다.

    ```yaml
    spring:
    	h2:
    		console:
    			path:/h2-test
    ```

- 프로퍼티값을 추가하는 것만으로도 자동 환경 설정에 자동으로 적용되어 애플리케이션을 실행합니다.
- 프로퍼티값 확인
    - [스프링 문서](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#common-application-properties)

### 2.5.3. 자동 설정 어노테이션 살펴보기

- 자동 설정을 위한 조건 어노테이션
    - @ConditionalOnBean : 해당 빈 클래스나 이름이 미리 빈 팩토리에 포함되어 있을 경우
    - @ConditionalOnClass : 해당 클래스가 클래스 경로에 있을 경우
    - @ConditionalOnCloudPlatform : 해당 클라우드 플랫폼이 활용 상태일 경우
    - @ConditionalOnExpression : SpEL에 의존하는 조건일 경우
    - @ConditionalOnJava : JVM 버전이 일치하는 경우
    - @ConditionalJndi : JNDI가 사용 가능하고 특정 위치에 있는 경우
    - @ConditionalOnMissingBean : 해당 빈 클래스나 이름이 미리 빈 팩토리에 포함되지 않은 경우
    - @ConditionalOnMissingClass : 해당 클래스가 클래스 경로에 없을 경우
    - @ConditionalOnWebApplication : 웹 애플리케이션인 경우
    - @ConditionalOnNotWebApplication : 웹 애플리케이션이 아닌 경우
    - @ConditionalOnProperty : 특정 프로퍼티가 지정한 값을 갖는 경우
    - @ConditionalOnResource : 특정한 리소스가 클래스 경로에 있는 경우
    - @ConditionalOnSingleCandidate : 지정한 빈 클래스가 이미 빈 팩토리에 포함되어 있고 단일 후보지로 지정 가능한 경우

- 자동 설정을 위한 순서 어노테이션
    - @AutoConfigureAfter : 지정한 특정 자동 설정 클래스들이 적용된 이후에 해당 자동 설정 적용
    - @AutoConfigureBefore : 지정한 특정 자동 설정 클래스들이 적용되기 이전에 해당 자동 설정 적용
    - @AutoConfigureOrder : 자동 설정 순서 지정을 위한 스프링 프레임워크의 @Order 변형 어노테이션. 기존의 설정 클래스에는 영향을 주지 않고 자동 설정 클래스들 간의 순서만 지정

- H2ConsoleAutoConfiguration.class

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass(WebServlet.class)
@ConditionalOnProperty(prefix = "spring.h2.console", name = "enabled", havingValue = "true", matchIfMissing = false)
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
@EnableConfigurationProperties(H2ConsoleProperties.class)
public class H2ConsoleAutoConfiguration {

	private static final Log logger = LogFactory.getLog(H2ConsoleAutoConfiguration.class);

	@Bean
	public ServletRegistrationBean<WebServlet> h2Console(H2ConsoleProperties properties,
			ObjectProvider<DataSource> dataSource) {
		String path = properties.getPath();
		String urlMapping = path + (path.endsWith("/") ? "*" : "/*");
		ServletRegistrationBean<WebServlet> registration = new ServletRegistrationBean<>(new WebServlet(), urlMapping);
		H2ConsoleProperties.Settings settings = properties.getSettings();
		if (settings.isTrace()) {
			registration.addInitParameter("trace", "");
		}
		if (settings.isWebAllowOthers()) {
			registration.addInitParameter("webAllowOthers", "");
		}
		dataSource.ifAvailable((available) -> {
			try (Connection connection = available.getConnection()) {
				logger.info("H2 console available at '" + path + "'. Database available at '"
						+ connection.getMetaData().getURL() + "'");
			}
			catch (SQLException ex) {
				// Continue
			}
		});
		return registration;
	}

}
```

- @ConditionalOnWebApplication(type = Type.SERVLET)
    - 웹 애플리케이션일 때
- @ConditionalOnClass(WebServlet.class)
    - WebServlet.class가 클래스 경로에 있을 때
- @ConditionalOnProperty(prefix = "spring.h2.console", name = "enabled", havingValue = "true", matchIfMissing = false)
    - spring.h2.console.enabled = true

### 2.5.4. H2 Console 자동 설정 적용하기

```groovy
runtimeOnly 'com.h2database:h2'
```

#### runtime vs compile
- 런타임(Runtime)
    - 컴파일이 끝난 후 프로그램은 사용자에 의해 실행이 되며 이 실행(동작)될때를 런타임이라고 한다.
- 컴파일(compile)
    - 개발자가 소스코드를 작성하고 컴파일 과정을 통해 기계어 코드로 변환 되어 실행 가능한 프로그램이 된다.

- 빈을 등록해 H2 콘솔 사용

```java
package starter.spring.boot.config;

import javax.servlet.Servlet;
import javax.servlet.annotation.WebServlet;

import org.springframework.boot.autoconfigure.web.ServerProperties;
import org.springframework.boot.autoconfigure.web.servlet.WebMvcProperties;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class DataSourceConfig {
    
    @Bean
    ServletRegistrationBean h2servletRegistration(){
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(new CustomServlet());
        registrationBean.addUrlMappings("/console/*");
        return registrationBean;
    }
}
```

- 예전에는 이런 방식으로 했었지만, 스프링 부트 자동 설정으로 인해 단순히 설정 프로퍼티 값만 변경하면 됩니다.

```yaml
spring:
  h2:
    console:
      enabled: true
      path: /h2-console
  datasource:
    url: jdbc:h2:mem:testdb
```

- H2 DB는 메모리 데이터베이스로 보통 테이스용으로만 사용합니다. 주 저장소가 아니기 때문에 불필요하기 컴파일 의존성에 포함될 필요가 없습니다. 따라서 런타임으로 변경해주는 것이 좋다.

[Spring H2 Database Properties](https://github.com/ksy90101/TIL/blob/master/spring/h2-properties.md)

## 2.6. 마치며

- 자동 환경 설정만큼은 내부를 이해해두는 것이 좋다. 다양한 자동화 설정을 적용할 때 무엇이 적용되었는지 확인하고 원하는 최적의 설정을 반영할 수 있기 때문이다.

