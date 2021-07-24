# 1장. 도메인 모델 시작

## 도메인

- 도메인(domain)이란 소프트웨어로 해결하고자 하는 문제 영역이다.
- 도메인은 여러 하위 도메인으로 구성된다.

  ![image](https://user-images.githubusercontent.com/53366407/124347415-a5482e00-dc1f-11eb-8a4f-9e1ba3e48d2f.png)

- 그러나 특정 도메인을 위한 소프트웨어라고 해서 도메인이 제공해야 할 모든 기능을 구현 하는 것은 아니다. 예시로는 외부 서비스를 사용하는 경우가 될 것이다.
- 또한 도메인마다 고정된 하위 도메인이 존재하는 것이 아니다. 수작업으로 문제를 해결하는 경우도 있다.
- 즉, 어떻게 하위 도메인을 구성할지 여부는 상황에 따라 달라진다. 예를들어 일반고객을 위한 것이냐, 기업 고객을 위한 것이냐에 따라 달라진다.

## 도메인 모델

- 도메인 모델이란 특정 도메인을 개념적으로 표현한 것이다.
- 도메인 모델을 사용하면 여러 관계자들이 동일한 모습으로 도메인을 이해하고 도메인 지식을 공유하는 데 도움이 된다.
- 도메인을 이해하려면 도메인이 제공하는 기능과 도메인의 주요 데이터 구성을 파악해야 하는 데, 이런 면에서 기능과 데이터를 함께 보여주는 객체 모델은 도메인을 모델링하기에 적합하다.
- 도메인을 이해하는 데 도움이 된다면 표현 방식이 무엇인지 중요하지 않다.
    - 클래스 다이어그램
    - 상태 다이어그램
    - 그래프
    - 수학 공식
    - 등....

  ![image](https://user-images.githubusercontent.com/53366407/124347420-b1cc8680-dc1f-11eb-8bfa-fffc53837598.png)

### 하위 도메인과 도메인 모델

- 모델의 각 구성요소는 특정 도메인을 한정할 때 비로소 의미가 완전해지기 때문에, 각 하위 도메인마다 별도로 모델을 만들어야 한다.

ORder.save()

도메인 안에 

## 도메인 모델 패턴

- 일반적으로 애플리케이션은 4개의 계층으로 이루어져 있다.
    1. 사용자인터페이스(UI) 또는 표현(Presentation)
        - 사용자 요청을 처리하고 사용자에게 정보를 보여준다. 사용자뿐만 아니라 외부 시스템도 포함된다.
    2. 응용(Application)
        - 사용자가 요청한 기능을 실행한다. 업무 로직을 직접 구현하지 않으며 도메인 계층을 조합해서 기능을 실행한다.
    3. 도메인
        - 시스템이 제공할 도메인의 규칙을 구현한다.
    4. 인프라스트럭처(Infrastructre)
        - 데이터베이스나 메시징 시스템과 같은 외부 시스템과의 연동을 처리한다.
- 도메인 모델은 아키텍처상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴을 말한다.
- 즉, 도메인 규칙을 객체 지향 기법으로 구현하는 패턴이 도메인 모델 패턴이다.
- 핵심 규칙을 구현한 코드는 도메인 모델에만 위치하기 때문에 규칙이 바뀌거나 규칙을 확장해야 할 때 다른 코드에 영향을 덜 주고 변경 내역을 반영할 수 있게 된다.

### 도메인 모델?

- 도메인 자체를 표햔하는 개념적인 모델을 의미하지만, 도메인 계층을 구현할 때 사용하는 객체 모델도 도메인 모델이라고 한다.

### 개념 모델 vs 구현 모델

- 개념 모델은 순수하게 문제를 분석한 결과물로 DB, 트랜잭션, 성능 등을 고려하지 않은 모델이다.
- 구현 모델은 개념 모델을 기반으로 구현한 모델이다.
- 처음부터 개념 모델을 완벽하게 만들 수 없다. 전반적인 개요를 알 수 있는 수준으로 작성해 전체 윤곽을 이해하는데 집중하고 구현하는 과정에서 구현 모델로 점진적으로 발전해 나가는 것이 중요하다.

## 도메인 모델 도출

- 도메인을 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소, 규칙, 기능을 찾는 것이다.
- 도메인을 구현하다 보면 특정 조건이나 상태에 따라 제약이나 규칙이 달리 적용되는 경우가 많다. → 예외 처리
- 모델을 공유할 때는 화이트보드나 위키와 같은 도구를 사용해서 누구나 쉽게 접근할 수 있도록 하면 좋다.

### 문서화

- 문서화를 하는 주된 이유는 지식을 공유하기 위함이다.
- 코드를 보면서 도메인을 깊게 이해하게 되므로 코드 자체도 문서화의 대상이 된다.

## 엔티티와 밸류

### 엔티티(Entity)

- 가장 큰 특징은 식별자를 갖는다는 것으로 식별자는 엔티티 객체마다 교유해서 각 엔티티는 서로 다른 식별자를 갖는다.
- 엔티티의 식별자는 바뀌지 않고 고유하기 때문에 두 엔티티 객체의 식별자가 같으면 두 엔티티는 같다고 판단할 수 있다. 자바에서는 클래스에서 식별자를 이용해 equals()와 hashCode() 메서드를 구현할 수 있다.

### 엔티티의 식별자 생성

- 아래와 같은 방식으로 생성이 가능하다.
    - 특정 규칙에 따라 생성
    - UUID(Universally Unique IDentifer) 사용
    - 값을 직접 입력
    - 일련번호 사용(시퀀스나 DB의 자동 증가 칼럼 사용)
- 자동 증가 컬럼은 DB 테이블에 데이터를 삽입해야 비로소 값을 알 수 있기 때문에 테이블에 데이터를 추가하기 전에는 식별자를 알 수 없다. 즉, 엔티티 객체를 생성할 때 식별자를 전달할 수 없다.

### 밸류 타입

- 의미를 명확하게 표현하기 위해 사용한다. 기본 타입보다 Value 타입이 코드를 읽는 입장에서 더 정확히 다가온다.
- 또한 해당 타입을 위한 기능을 추가할 수 있다.
- 데이터 변경 기능을 제공하지 않는 타입을 불변이라고 표현하는데, Value 타입을 불변으로 구현하는 것이 좋다. 이유는 안전한 코드를 작성할 수 있기 때문이다.
- 불변 객체는 참조 투명성과 스레드에 안전한 특징을 갖고 있다.
- 엔티티 타입의 두 객체가 같은지 비교할 때 주료 식별자를 사용했지만, 밸류 타입은 모든 속성이 같은지 비교해야 한다.

### 엔티티 식별자와 밸류 타입

- 도메인에서 특별한 의미를 지니는 경우가 많기 때문에 식별자를 위한 밸류 타입을 사용해서 의미가 잘 드러나도록 할 수 있다.

### 도메인 모델에 set 메서드 넣지 않기

- set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게 만든다.
- 도메인이 스스로 상태와 행동을 결정하도록 하는 것이 중요하다. (객체지향 사실과 오해)
- 또한 도메인 객체가 생성 할 때 완전한 상태가 아닐수 있다는 것도 단점으로 꼽힌다.
- 도메인 객체가 불완전한 상태로 사용되는 것을 막으려면 생성 시점에 필요한 것을 전달해 주어야 한다. 즉, 생성자를 통해 필요한 데이터를 모두 받아야 한다.

### DTO의 get/set 메서드

- 최근 개발 프레임워크나 개발 도구는 set 메서드가 아닌 private 필드에 직접 값을 할당할 수 있는 기능을 제공하기 때문에 불변을 유지할 수 있다.

## 도메인 용어

- 코드에 도메인 용어를 반영하면 코드를 도메인 용어로 해석하는 과정이 줄어들어 가독성을 높이고 코드를 분석하고 이해하는 시간을 절약한다.