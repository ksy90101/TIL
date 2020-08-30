# 1장. 스프링부트 입문하기

## 1.1. 스프링 부트 소개
- 환경 설정을 최소화하여 비지니스 로직에 집중할 수 있도록 하여 생산성을 향상 시킨다.
- Spring Boot Starter
    - 애플리케이션에 포함되는 편리한 의존성의 집합체
    - 의존성을 찾지 않아도 모든 필요한 스프링 관련 기술들을 한번에 추가해준다.

### 1.1.1. 스프링 부트 특징
- 간단하고 자동화된 설정으로 빠르게 실행
- Embed Tomcat, Jetty, UnderTow와 같은 WAS를 사용하여 독립 실행이 가능
- 통합 스타터 제공으로 Maven / Gradle 구성 최소화
- XML 설정 요구를 하지 않음.
- JAR를 사용하여 자바 옵션만으로 배포 가능
- Spring Actuator 제공
    - 모니터링 기능

### 1.1.2. 스프링 부트와 스프링
- 스프링 > 스프링 부트
- 사용자 <-> 스프링부트 <-> 스프링

## 1.2. 스프링 부트 스타터 들여다보기
- 스타터의 명명 규칙
    > spring-boot-starter-*

## 1.2.1. 스타터 내부의 의존성 확인
- [Spring Boot Reference Guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)
- Spring Boot Starter
    - Spring Boot : 스프링 부트에서 기본 제공하는 의존성
    - Spring Boot AuthConfigure : 스프링 부트의 자동 환경 구성에 필요한 의존서ㅏㅇ
    - Spring Boot Starter Logging
    - Javax Annotation API
    - Spring Core
    - Snakeyaml: yaml

- Intelli J
[start-spring-boot2-1-1](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-1-1.png?raw=true)

## 1.2.2. 스타터에 명시된 버전은 어떻게 확인 할까?
[spring-projects/spring-boot](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/build.gradle)
[Dependency versions](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-dependency-versions.html#dependency-versions)

## 1.2.3. 스프링 부트 버전에 따라 무엇이 다른지 어떻게 할 수 있을까?
[spring-projects/spring-boot](https://github.com/spring-projects/spring-boot/wiki)

## 1.2.3. 스프링 부트 장단점
- 장점
    - 각자의 의존성 버전을 올리기가 간편하다.
    - 특정 라이브러리에 버그가 있으면 버그 픽스 버전 받기가 편리하다.
    - 간단한 어노테이션 설정, 프로퍼티 설정으로 원하는 기능 적용
    - 내장 톰켓 기능
- 단점
    - 특정 설정을 커스터마이징을 하고 싶다면 설정 내부 코드를 봐야함.
    
## 참고 자료
- [처음 배우는 스프링부트2](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=168752840)

