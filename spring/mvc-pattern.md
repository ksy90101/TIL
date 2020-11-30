# MVC 패턴

## MVC 패턴이란?

- Model & View & Controller
- 디자인 패턴으로써 애플리케이션을 위의 3가지 역할로 구분해놓은 개발방법론입니다.

## 웹 애플리케이션의 아키텍처 - 모델 1

![mvc-pattern-1](https://github.com/ksy90101/TIL/blob/master/spring/img/mvc-pattern-1.png?raw=ture)

- JSP + JavaBean(Model)
- 가장 큰 특징은 뷰 + 로직이라고 생각하면 될거 같습니다.
- 따라서 장점으로는 구조가 간단해지지만, 단점으로 출력과 비즈니스 로직이 섞여서 코드가 복잡해지고 프론트와 백엔드의 분업이 용이하지 않다는 점이 있습니다.

## 웹 어플리케이션 아키텍처 - 모델 2 == MVC

![mvc-pattern-2](https://github.com/ksy90101/TIL/blob/master/spring/img/mvc-pattern-2.png?raw=ture)

- 출력과 비즈니스 로직이 섞여 코드가 복잡해지는 모델1의 단점을 보안하고자 모델2가 나오게되었습니다.
- JavaBean(Service) + JSP(View) + 서블릿(Controller)
- 장점으로는 뷰와 비즈니스 로직이 분리가 되어서 덜 복잡하고 분업이 용이해졌지만, 단점으로 러닝커브가 존재하고 작업량이 많아진다는 점이 있습니다.

### 흐름

1. 사용자는 원하는 기능을 처리하기 위한 모든 요청을 Contoller 에게 보낸다.
2. Controller는 Model을 사용하고, Model은 알맞은 비즈니스 로직을 수행하게 됩니다.
3. Controller는 사용자에게 응답할 View를 선택합니다.
4. 선택된 View는 사용자에게 알맞은 결과화면을 보여줍니다.
5. 이때, 사용자에게 보여줄 데이터는 Controller를 통해 받게 됩니다.

### Model

- 비즈니스 로직을 가지고 있는 객체입니다.

### View

- 사용자에게 보여줄 화면입니다.

### Controller

- 모델 객체로의 데이터 흐름을 제어합니다.
- 뷰와 모델의 역할을 분리를 하는 역할을 합니다.

## Why MVC?

- 원래는 개인용 컴퓨터에 작동하는 애플리케이션의 개발을 목적으로 만들어진 디자인패턴이지만, 웹 어플리케이션을 사용하기 위한 용도로 폭 넓게 사용되고 있습니다.

### 장점

- 각 컴포넌트의 코드 결합도를 낮춥니다.
- 코드의 재사용성이 올라갑니다.
- 구현자들 간의 커뮤니케이션 효율성을 높일 수 있습니다.

## 많이 하는 실수들

- Mode에서 View의 접근 또는 역할.
- View에서 일어하는 과한 값 검증과 예외 처리.
- View에서 일어나는 비즈니스 로직

### 실수 해결 방법

- Controller 내에는 비즈니스 로직을 최대한 배제하고 Model View 사이의 연결 역할만 하도록 구현한다.
- Controller에서 중복이 발생하면 별도의 객체로 분리하거나 별도의 메서드로 분리한다.

## Service

- 비즈니스 로직을 수행하는 메서드를 가지고 있는 객체입니다.
- 비즈니스 로직을 별도의 Service 객체에서 구현하도록 하고 Controller는 Service 객체를 사용하도록 합니다.
- Service는 Transcation을 가지게 됩니다.

## Repository(DAO, Data Access Object)

- 데이터를 접근하는 기능을 별도의 Repository나 DAO 객체에서 구현합니다.
- Service는 Repository 객체를 사용합니다.
- DAO vs Repository

[DAO vs Repository Patterns | Baeldung](https://www.baeldung.com/java-dao-vs-repository)

[What is the difference between DAO and Repository patterns?](https://stackoverflow.com/questions/8550124/what-is-the-difference-between-dao-and-repository-patterns)

## 참고자료

[[10분 테코톡] 👩🏻‍💻👨🏻‍💻해리&션의 MVC 패턴](https://www.youtube.com/watch?v=uoVNJkyXX0I&t=229s)
