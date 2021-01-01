# Spring Boot Welcome Page

## 서론

> Spring Boot supports both static and templated welcome pages. It first looks for an `index.html` file in the configured static content locations. If one is not found, it then looks for an index template. If either is found, it is automatically used as the welcome page of the application.

- 위의 spring boot docs에 따르면 static폴더의 index.html을 찾아 맨 처음 `/(루트)` view(Welcome Page)로 지정하며, static 폴더에 못찾을 경우 template 폴더까지 스캔 한다. 만약 둘다 없다면 자동적으로 애플리케이션 welcome page를 사용한다고 한다.
- 그렇다면 이게 무슨 이야기인가?

## static, templates 각 폴더에 index.html을 넣어서 확인해보기

### static 폴더에 index.html이 있다면?

- resources/static/index.html

```html
<!DOCTYPE HTML>
<html lang="ko">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>
```

![spring-boot-welcome-page-1](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-1.png?raw=ture)

- 위의 코드를 작성해 애플리케이션을 실행하면 사진과 같이 루트 페이지(웰컴 페이지)가 정상적으로 뜬걸 확인할 수 있다.

### templates 폴더에 index.html이 있다면?

- resources/templates/index.html

```html
<!DOCTYPE HTML>
<html lang="ko">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>
```

![spring-boot-welcome-page-2](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-2.png?raw=ture)

### static, templates 폴더에 둘다 있다면?

- resources/static/index.html

```html
<!DOCTYPE HTML>
<html lang="ko">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello Static
<a href="/hello">hello</a>
</body>
</html>
```

- resources/templates/index.html

```html
<!DOCTYPE HTML>
<html lang="ko">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello Templates
<a href="/hello">hello</a>
</body>
</html>
```

![spring-boot-welcome-page-3](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-3.png?raw=ture)

- 위의 사진처럼 static 폴더에 있는 값이 나오는 걸 확인할 수 있다.

### 두 폴더 모두 없다면?

![spring-boot-welcome-page-4](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-4.png?raw=ture)

- 위의 사진처럼 에러 메시지를 보내주게 된다.

## 동작원리

```java
public class WebMvcAutoConfiguration {
	private Optional<Resource> getWelcomePage() {
			String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
			return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
	}

	private Resource getIndexHtml(String location) {
			return this.resourceLoader.getResource(location + "index.html");
	}
}
```

- 위의 코드처럼 getWeblcomPage()로 index.html를 찾는다.
- 각 location들을 모두 들고와 index.html이 있는지 확인하게 된다.

```java
final class WelcomePageHandlerMapping extends AbstractUrlHandlerMapping {

	private static final Log logger = LogFactory.getLog(WelcomePageHandlerMapping.class);

	private static final List<MediaType> MEDIA_TYPES_ALL = Collections.singletonList(MediaType.ALL);

	WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders,
			ApplicationContext applicationContext, Optional<Resource> welcomePage, String staticPathPattern) {
		if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
			logger.info("Adding welcome page: " + welcomePage.get());
			setRootViewName("forward:index.html");
		}
		else if (welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
			logger.info("Adding welcome page template: index");
			setRootViewName("index");
		}
	}

	private void setRootViewName(String viewName) {
		ParameterizableViewController controller = new ParameterizableViewController();
		controller.setViewName(viewName);
		setRootHandler(controller);
		setOrder(2);
	}
}
```

- 위의 코드에서 welcomepage를 RootView에 설정해주게 된다.
- 그러나 static에 없다면 아래의 `welcomeTemplateExists()` 이 메서드를 통해 templates 폴더의 index의 cotroller를 만들게 된다.
- 따라서 둘다 없다면 값이 없기 때문에 에러메시지가 나오게 되는 것이다.

### static 폴더에서의 디버깅

![spring-boot-welcome-page-5](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-5.png?raw=ture)

- 각각의 locations들을 확인할 수 있고 locations들을 돌면서 index.html을 찾게 된다.

![spring-boot-welcome-page-6](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-6.png?raw=ture)

![spring-boot-welcome-page-7](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-7.png?raw=ture)

- ParameterizableViewController에 매핑하는 걸 확인할 수 있다.

### tempaletes 폴더에서의 디버깅

![spring-boot-welcome-page-8](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-8.png?raw=ture)

![spring-boot-welcome-page-9](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-welcome-page-9.png?raw=ture)

- ParameterizableViewController에 매핑하는 걸 확인할 수 있다.
