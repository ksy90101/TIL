# 1주차 세션 정리

## 서론

- ****[[스터디/12기] 단순 CRUD는 그만! 웹 백엔드 시스템 구현(Spring Boot)](https://programmers.co.kr/learn/courses/13469) 이 스터디를 참여해서 쓴 글입니다.**
- 1주차 세션에 대한 내용은 아래와 같습니다.
    
    ### **🗓 1주차**
    
    ### **내 손으로 직접 Maven 기반 Spring Boot 프로젝트 셋업하기**
    
    ### **1주차 미션 핵심**
    
    - ✅ Spring 주요 개념 (이미 알아도 다시)제대로 짚고 가기
    - ✅ Spring Boot 개론(Spring 3.* 버전만 사용해본 경우, 필수 내용)
    - ✅ Maven 프로젝트 직접 세팅
    
    IDE는 IntelliJ, Eclipse 둘 중 본인이 편한 것을 써도 된다. 또한, 프로젝트 셋업은 모두 준비된 것을 주어주지 않는다. 앞으로의 미션 수행을 위해 **직접 세팅하는 것** 이 중요하다. 매번 누가 던져준 환경에서만 개발해본 경우에는 꼭 초반에 셋업 부분을 제대로 이해하고 넘어가야 한다.
    
- 스터디 과정은 페이스북과 같은 게시판 서비스를 만들어 보는것이며, 게시판 기능을 만들 때 매주 마다 기능을 추가해 나가면서 개선해 나가는게 목적입니다.
- 1주차 미션은 간단한 유저 등록 / 조회 기능을 하는 프로젝트 입니다

## 스프링이란?

## 스프링이란?

강의에서 다루는 내용은 JDBC, Security, Spring MVC 정도입니다.

### 스프링 프레임워크 실행 환경

![image](https://user-images.githubusercontent.com/53366407/156082438-1aef7bea-d0ff-4e2f-b2d8-7b0f95bedee7.png)

스프링의 모듈은 엄청 많고, 애플리케이션은 이것들을 조합해서 사용합니다.

## 프로젝트를 먼저 보는 곳

- maven을 쓴다면, pom.xml을 먼저 보고, gradle을 사용한다면 build.gradle을 먼저 살펴봅니다.
- 어떤 라이브러리를 사용하고, 어떤 버전을 사용하는지 확인하기 위해서 입니다.
- 그 다음에는 Bean 설정을 확인합니다.

## 스프링의 주요 개념

![image](https://user-images.githubusercontent.com/53366407/156082502-adb6aadb-f9f8-4352-8abb-d7f47489eb61.png)

- 이 코드의 문제는 무엇인가?
    - 이 코드는 드라이버를 가져오고, SQL 쿼리를 실행하고, 값을 담고 업데이트 하고, 커넥션을 닫고 등등 즉, 너무 하는 일이 많다.

### 관심사의 분리 (Separation of Conecerns)

> 프로그래밍의 기초 개념 중 하나로 객체지향에 적용해보면 관심이 같은 것 끼리는 하나의 객체 안으로 또는 친한 객체로 모이게 하고, 관심이 다른 것은 가능한 한 따로 떨어져서 서로 영향을 주지 않도록 분리하는 것
> 
- 서로 영향을 주지 않도록 분리하기 위해서는 인터페이스를 사용할 수 있다.

![image](https://user-images.githubusercontent.com/53366407/156082542-ea123f86-2766-45b4-8fae-f8055a2201d3.png)

![image](https://user-images.githubusercontent.com/53366407/156082550-7a0fab82-aea7-44b3-bc71-b1c815ad116c.png)
- 드라이버로드 커넥션 생성 sql호출 여러개 관심사 하나의 dao 클래스 안에서 다 처리하니까 커넥션 만드는부분만 추출을해야하는데 벤더마다 구현이 다를수있다

### 아직도 문제점은 남아있다.

- 위 코드에서 문제점은 무엇일까?
- 바로 ConnectionMaker를 직접 UserDao에서 생성하고 있다. 이러면 결합도가 굉장히 강해지게 된다. 코드를 작성하는 시점에 결합되어 컴파일 타임에 의존성을 갖게 되는데, 이걸 약하게 풀어주기 위해서는 생성자를 통해 외부에서 구현체를 받아오는 방법을 사용하면 된다.

![image](https://user-images.githubusercontent.com/53366407/156082575-0155e57c-91bc-49bf-b3c3-127cb428c34b.png)

### 아직도 문제점은 남아있다.

- Test에서도 현재 USerDao를 생성하고 있는데, Factory를 만들면, 처리할 수 있다.

![image](https://user-images.githubusercontent.com/53366407/156082583-71b053cd-2aeb-40a1-8627-f81fe7e2dcf4.png)

## 제어의 역전(Inversion Of Controller)

- 일반적으로 프로그램은 시작되는 시점에 사용할 객체를 결정하고, 생성하고, 호출하는 반복되는 작업을 한다.
- 모든 객체가 능동적으로 자신이 사용할 클래스를 결정하고 생성하는데, Spring은 제어의 역전으로 인해 사용을 스스로 결정하지 않고 생성하지도 안흔낟.
- 즉, 모든 제어 권한을 자신이 아닌 다른 대상에게 위임하는 것이다.
- 위에서는 DaoFactory가 결정해서 UserDao에 사용하라고 하는 것이다.
- 즉, 프레임워크에서 템플릿처럼 만들어져 있고 구멍구멍 뚫어서 그 구멍을 채우면서 우리는 개발하고 있다고 생각하면 된다.

### 그래서 Spring은 Applcation Context를 지원해준다.

![image](https://user-images.githubusercontent.com/53366407/156082656-97164d9f-c455-4531-9836-864020f864e7.png)

- 스프링은 BeanFactory와 ApplicationContext로 IoC 컨테이너를 제공해준다. (위에서 작성한 DaoFactory라고 생각하면 된다.)

### 그렇다면 관리하는 객체를 뭐라고 부르냐? - 빈(Bean)

- 스프링에 의해 IoC 방식으로 관리되는 객체를 빈이라고 한다.
- 빈은 Java Annotation(@Configuration, @Bean)이나, XML로 설정을 한다.

## Servlet Container

- 스프링은 기본적으로 서블릿 기반으로 웹 서비스를 제공한다.
- Servelt Container - WAS - Tomcat 으로 동작하며, 각 Servlet들이 있다.

![image](https://user-images.githubusercontent.com/53366407/156082672-b28c597b-13a1-46db-b517-67e1b8d090d2.png)

- 실제로 우리는 Application Context를 만들어야 하는데, 시작할때 알아서 스프링이 생성이 된다.
- ServletContext가 생성이 되고, 각 서블릿에 Bean으로 등록되는데 이걸 Dispatcher Servelt에서 빈을 주입해준다. 이걸 프론트 컨틀롤러 패턴이라고 한다.

빈이 어떻게 만들어 지는지 메타

## 웹 환경에서 스프링 애플리케이션이 동작하는 방식

![image](https://user-images.githubusercontent.com/53366407/156082687-67a7b10e-61be-430b-a70c-f24b2d050c6d.png)

- DispacherServlet은 하나의 WebApplicationContext하나와 Mapping되어 잇다.
- DispatcherServlet이 Controller를 찾아서 호출한다.
- WebApplciationContext에 있는 Bean을 DispatcherServelt이 사용한다.

> 이걸 알아서 해준다. (URL Pattern에 맞는 Controller 액션을 찾아서 사용한다.)
> 

## 3계층 아키텍처

![image](https://user-images.githubusercontent.com/53366407/156082704-4866c0df-8113-4ba9-b855-7b7735f3a254.png)

- 프리젠테이션 계층을 Spring MVC라고 한다.
- 여기서 Controller부분을 Servelt이라고 한다.
- 서비스 계층과 데이터 엑세스 계층은 root ApplicationContext에 있다.
- 따라서 아래와 같은 계층 구조를 가지고 있다.

![image](https://user-images.githubusercontent.com/53366407/156082722-21209adc-7b72-492e-a19a-a558b257ebd6.png)

- 3개의 URL 패턴이 있다면 하나의 URL를 하나만 시작할때 로드하고, 나머지는 요청할때 로드하도록 처리할 수 있는데, 이걸 load-on-startup 옵션이나 서블릿을 쪼개서 사용하는 방법인데, 요즘에는 MSA 개념이 있어 MSA로 진행한다.

## 스프링 부트의 특징

- SpringApplication을 통한 손 쉬운 실행
- **Auto Configuration**
- 쉬운 외부 환경 설정 - Properties, YAML, Command Line 설정 등
- 프로파일을 통한 실행환경 관리
- Packaging Executable Jar
- Developer Tools
- **Embedded Servelt Containers**

### Auto Configuration

- EnabledAutoConfiguration (AutoConfiguration을 사용하겠다는 의미)
- AutoConfigurationImportSelect(실제 Import를 할것을 찾는 것
- Spring.factories에서 AutoConfiguration을 해주는 것들이 있는데, Select해서 찾습니다.

## 기타 이야기

- Entity에서는 도메인 로직이 들어가야 하며, 어떤 필드는 불변으로 해야하는지 고민 해보야 한다.
- VO나, Validation을 도입해야 할지 고민을 해보자.
- DTO를 만들어 데이터를 통신하자.
    - 비지니스 코드에 View에 대한 코드가 들어가기 때문이다.
- 비지니스 룰은 중요하다. 비지니스 룰을 모르겠다면, 물어보는게 가장 중요하다.
- 테스트는 단위테스트를 먼저 작성하고 통합테스트를 작성하는 것이 좋다.
