# Spring Intiializr프로젝트가 spring-boot-starter-test 에서 junit-vintage-engine가 exclude되어 있는 이유

```groove
testImplementation('org.springframework.boot:spring-boot-starter-test') {
    exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
}
```

- Spring Intiializr로 프로젝트를 만들면 spring-boot-starter-test 에서 항상 junit-vintage-engine은 exclude가 되어있다.
- vintage가 무엇이고 junit-vintage-engine이 무엇이길래 exclude가 되는지 알아보자.

## Junit5

- JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage
- 위와 같이 Junit5는 3가지 하위 프로젝트의 여러 모듈로 구성되어 있다.
- 테스트 작성자를 위한 API 모듈과 테스트 실행을 위한 API가 분리되어 있다.

### JUnit Platform

- JVM 기반 테스트 프레임워크를 실행시키기 위한 모듈
- 또한 JUnit4 환경에서도 플랫폼 모듈을 진행하는 테스트를 위해 Runner를 제공한다.

### JUnit Jupiter

- JUnit5에서 테스트 및 확장을 작성하기 위한 프로그래밍 모델 + 확장 모델이다.

### JUnit Vintage

- JUnit3, JUnit4 기반의 테스트를 JUnit Platform에서 실행시키기 위한 TestEngin을 제공하는 모듈

## 모듈 구성

![spring-boot-starter-test-exclude-vintage-1](https://github.com/ksy90101/TIL/blob/master/spring/img/spring-boot-starter-test-exclude-vintage-1.png?raw=ture)

## exclude 이유

- `vintage`는 이름에서 보이듯이 예전 버전과의 호환을 위해 존재한다. spring-boot-starter-test는 JUnit4를 가지고 있으며, 개발자가 선택해서 테스트 코드를 작성할 수 있게 한다.
- JUnit5를 지원하는데 굳이 JUnit3, 4의 모듈까지는 가지고 있을 필요가 없다고 보는 것이다.
- 혹시라도 JUnit3, 4를 사용해서 테스트 코드를 작성할 것이면 exclude를 제거하면 된다.

## 참고자료

[JUnit 5 소개](https://javacan.tistory.com/entry/JUnit-5-Intro)

[JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)
