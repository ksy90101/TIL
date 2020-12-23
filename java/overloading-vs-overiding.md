# Overloading(오버로딩) VS Overriding(오버라이딩)

## 서론

- Java 개발자로 면접 볼 시 문제로 많이 나오는 개념으로써, 자주 사용하지만 이름과 개념에 대해 정확한 정리가 없어 이야기 하기가 힘들때가 있어서 이번 기회에 정리를 해보려고 합니다.
- 이 두개는 다형성을 지원하기 위해 나왔습니다.

## Overloading(오버로딩)

> 함수 오버로드(영어: Function overloading)는 다양한 에이다(Ada), C#, C++, 자바(Java) 등의 다양한 프로그래밍 언어에서 사용되는 함수의 특징으로, 같은 함수 이름을 가지고 있으나 매개변수, 리턴타입 등의 특징은 다른 여러개의 서브프로그램 생성을 가능하게 한다. (C언어는 지원하지 않는다)

- 위키 백과

### 간단한 정리

- `같은 이름의 메소드`를 여러 개 가지면서 `매개변수의 유형과 개수`가 다르게 정의할 수 있습니다.
- 즉, 같은 이름 메소드는 여러개 있고, 각 메소드들의 다른점은 매개변수 유형과 개수가 다른 것 입니다.
- 메소드 뿐만 아니라 생성자도 오버로딩을 통해 다형성을 구현할 수 있다.

### 예제

- 생성자

```java
import java.time.LocalDate;

public class User {
    private String name;
    private String email;
    private int age;
    private LocalDate birthDay;

    public User(String name, String email, int age, LocalDate birthDay) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.birthDay = birthDay;
    }

    public User(String name, String email, LocalDate birthDay) {
        this.name = name;
        this.email = email;
        this.birthDay = birthDay;
    }

    public User(String name, String email, String age) {
        this.name = name;
        this.email = email;
        this.age = Integer.parseInt(age);
    }
}
```

- 위의 코드와 같이 매개변수의 갯수와 매개변수의 유형(타입)에 따라 다른 생성자를 만들수 있다.

- 메소드

```java
import java.time.LocalDate;

public class User {
    private String name;
    private String email;
    private int age;
    private LocalDate birthDay;

    public void update(String name){
        this.name = name;
    }

    public void update(String name, String email, int age){
        this.name = name;
        this.email = email;
        this.age = age;
    }

    public void update(String name, String email, String age){
        this.name = name;
        this.email = email;
        this.age = Integer.parseInt(age);
    }
}
```

- 위의 코드와 같이 매개변수의 갯수와 매개변수의 유형(타입)에 따라 다른 메소드를 만들 수 있다.

## Overriding(오버라이딩)

> 메소드 오버라이딩,(method overriding)은 객체 지향 프로그래밍에서 서브클래스 또는 자식 클래스가 자신의 슈퍼클래스들 또는 부모 클래스들 중 하나에 의해 이미 제공된 메소드를 특정한 형태로 구현하는 것을 제공하는 언어의 특징이다. 서브클래스에서의 구현은 부모 클래스에서 같은 이름, 같은 파라미터 또는 시그니처 그리고 같은 반환형을 갖는 메소드를 제공함으로써 슈퍼클래스에서의 구현을 오버라이드한다. 실행되는 메소드의 버전은 이것을 발생시키는데 사용되는 객체에 의해서 결정될 것이다. 만약 부모 클래스의 객체가 메소드를 발생시키는데 사용된다면 부모 클래스 버전이 실행될 것이지만, 만약 서브클래스의 객체가 메소드를 발생시키는데 사용된다면 자식 클래스 버전이 실행될 것이다. 몇몇 언어들은 프로그래머가 메소드를 오버라이딩하는 것을 예방하게할 수 있게 한다.

- 위키백과

### 간단한 정리

- `상위 클래스가 가지고 있는 메소드`를 `하위 클래스가 재정의`해서 사용합니다.
- 생성자가 아닌 메소드만 오버라이딩이 가능합니다.
- 대표적으로 모든 Class는 Object를 상속 받고 있어 toString(), equals(), hashCode()의 메소드를 재정의 할 수 있습니다.
- 위와 같은 메소드들에 의해 우리는 손쉽게 오버라이딩을 사용하게 됩니다.

### 예제

```java
import java.util.Arrays;

public enum Operator {
    ADDITION("+") {
        @Override
        public double apply(final double operand1, final double operand2) {
            return operand1 + operand2;
        }
    },
    SUBTRACTION("-") {
        @Override
        public double apply(final double operand1, final double operand2) {
            return operand1 - operand2;
        }
    },
    MULTIPLICATION("*") {
        @Override
        public double apply(final double operand1, final double operand2) {
            return operand1 * operand2;
        }
    },
    DIVISION("/") {
        @Override
        public double apply(final double operand1, final double operand2) {
            if (Double.isInfinite(operand1 / operand2)) {
                throw new ArithmeticException("0으로 나눌수 없습니다.");
            }
            return operand1 / operand2;
        }
    },
    ;

    private final String textOperator;

    Operator(final String textOperator) {
        this.textOperator = textOperator;
    }

    public static Operator of(final String symbol) {
        return Arrays.stream(values())
                .filter(operator -> operator.isTextOperator(symbol))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("해당 연산자를 찾을수 없습니다."));
    }

    public abstract double apply(double operand1, double operand2);

    private boolean isTextOperator(final String symbol) {
        return textOperator.equals(symbol);
    }
}
```

- 위와 같이 추상 메서드를 이용해 Overriding을 사용할 수 있습니다.

```java
import java.time.LocalDate;

public class User {
    private String name;
    private String email;
    private int age;
    private LocalDate birthDay;

    public User(String name, String email, int age, LocalDate birthDay) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.birthDay = birthDay;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", age=" + age +
                ", birthDay=" + birthDay +
                '}';
    }
}
```

- 또한 Object 객체에 있는 toString() 메서드를 오버라이딩으로 사용할 수 있습니다.

## 결론

- 오버로딩은 `같은 이름의 메소드`를 여러 개 가지면서 `매개변수의 유형과 개수`가 다르게 정의할 수 있습니다.
    - 장점으로는 메서드 이름이 같다면 같은 기능이라고 예측을 할 수 있으며, 같은 기능을 하는 메서드를 하나의 이름으로 정의함으로써 생산성을 높이는 역할을 할 수 있다고 생각합니다.(매번 다른 메서드 명을 고민할 필요가 없으니깐요.)
    - 단점으로는 너무 많은 오버로딩은 클라이언트의 코드를 작성하거나 읽기 어렵습니다. 예를들면 오버로딩된 생성자 5개가 있다면 각각이 어떤 의미가 있는지에 대해 해석하는 것이 쉽지가 않습니다.
- 오버라이딩은 상위 클래스에서 있는 메서드를 하위 클래스에서 재정의해서 사용할 수 있습니다.
    - 장점으로는 같은이름으로 로직만 변경하여 내용을 수정하여 사용할 수 있으므로 중복코드가 제거될수 있으며 메소드 하나로 여러 객체를 다루고 객체마다 같은 메소드명 이지만 다른 기능을 사용할 수 있다는 장점이 있다고 생각합니다.
