# OCP와 전략패턴

## if-else문의 문제점

- 변경 또는 확장이 될 수록 코드가 복잡해진다.
- 리팩토링 시 리팩토링 부분을 찾는데 점점 오래 걸리게 된다.
- 실수로 추가하지 않고 누락하는 부분이 생길 가능성이 있다.

- 위의 총 3가지 문제점을 한마디로 표현하지만 유지보수가 점점 어려워진다 라고 정의할 수 있겠습니다.
- 계속되는 기능 추가로 복잡도는 증가하면서 추가 수정이 힘들어 지는데, If-else를 사용하게 된다면 점점 커져서 한 메서드에 수백 줄 이상으로 빠르게 증가할 가능성이 있게 되면서 코드는 복잡하게 됩니다.

## OCP란?

- Open-Closed Principle
- 개방 폐쇄 원칙
- 소프트웨어 구성 요소(컴포넌트, 클래스, 모듈, 함수 등)는 확장에 대해서는 개방되어 있어야 하며 변경에 대해서는 폐쇄되어야 한다.
- 즉, 기존의 코드는 변경하지 않으면서 기능을 추가할 수 있도록 설계되어야 한다는 뜻입니다.

### 적용방법

- 상속(is-a)
    - 상위클래스와 하위 클래스에 강한 응집력으로 단점이 존재한다.
    - 즉, 깨지기 쉬운 상위 클래스 문제라고 하는데 상위가 변경되면 하위에 영향이 매우 크다는 의미입니다.
- 컴포지션(has-a)
    - 변경(확장) 될 것과 변하지 않을 것을 엄격히 구분한다는 장점이 있다.
    - 두 모듈을 만나는 지점에 인터페이스를 정의하는 방식으로 구현에 의존하는 것이 아니라 정의한 인터페이스에 의존하도록 코드를 작성한다고 생각하면 됩니다.
- 이펙티브 자바 3판 `아이템 18. 상속보다는 컴포지션을 사용하라` 에서 보듯이 상속보다는 컴포지션을 더 권장하고 있기 때문에 컴포지션을 효율적으로 할 수 있는 전략패턴에 대해서 설명하겠습니다.

### 컴포지션 적용 방법

1. 변경될 것과 변하지 않을 부분을 구분하여 변경될 부분을 인터페이스로 추출합니다.
2. 모듈이 만나는 지점에 인터페이스를 정의합니다.
3. 인터페이스에 의존하도록 코드를 작성합니다.

- 여기서 Java에서 대표적으로 볼 수 있는 예제는 List 인터페이스가 될거 같습니다. List 인터페이스로 ArrayList, LinkedList를 생성하는 것을 자주 사용하게 됩니다.

## 전략 패턴

- OCP를 준수하기 위한 디자인 패턴
- 즉, 컴포지션 적용 방법자체도 전략패턴이라고 볼 수 있습니다.
- 그렇다면 여기서 전략이라는 단어의 뜻을 조금 봐보면 좋을거 같습니다.

전략이란 어떤 목적을 달성하기 위해 일을 수행하는 방식.
비즈니스 규칙, 문제를 해결하는 알고리즘 등을 이야기 합니다.

- 인식은 하지 못했을지라도 기본적으로 가장 많이 사용하고 있는 디자인패턴이기 때문에 `디자인 패턴의 꽃` 이라는 별명이 붙어 있습니다.
- 말 그대로 전략을 쉽게 변경할 수 있도록 해주는 디자인 패턴으로 행위를 클래스로 캡슐화해 동적으로 행위를 자유롭게 바꿀 수 있게 해주는 패턴입니다.
- 가장 큰 장점은 새로운 기능의 추가가 기존의 코드에 영향을 미치지 못하게 되면서 OCP를 만족하게 됩니다.

### 전략패턴 예제1

- 사칙연산을 계산하는 메서드가 있다고 해봅시다.

```jsx
public class Calculator {

    public int calculate(final String operator, final int operand1, final int operand2) {
        if ("+".equals(operator)) {
            return operand1 + operand2;
        } else if ("-".equals(operator)) {
            return operand1 - operand2;
        } else if ("*".equals(operator)) {
            return operand1 * operand2;
        } else if ("/".equals(operator)) {
            return operand1 / operand2;
        }

        throw new IllegalArgumentException("계산할 수 없는 연산자 입니다.");
    }
}
```

- 위와 같은 코드는 실제 한 메서드가 길어 지게 되고 또한 새로운 조건이 추가되면 확장성이 떨어지게 됩니다.
- 이미 위에서 if-esle문에 대한 단점들은 많이 이야기 했기 때문에 위 내용을 참고하면 될거 같습니다.
- 그렇다면 실제 OCP를 지키면서 전략패턴를 구현하는 방법은 어떻게 될까요?

```jsx
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

- 위의 코드처럼 Enum을 이용해서 구현하게 된다면 새로 추가 해도 enum 값만 추가하면 되고 삭제가 된다 한들 다른 로직에 영향을 안 미치게 될 거 같습니다.

### 전략패턴 예제2

- 자동차 경주 게임을 실행하는데 자동차가 움직이는 조건이 4보다 큰 수가 나오면 전진을 하고 작은 수가 나온다면 멈춰있다고 했을때 숫자를 뽑는 것을 랜덤으로 한다고 생각해봅시다.

```jsx
public int moveNumber() {
        return (int)(Math.random() * 10);
	}
```

- 위와 같은 코드는 해당 메서드를 테스트 하기가 굉장히 어렵습니다. 또한 자동차 경주 게임에서 전진하는 값을 제어하는 것이 가장 중요하게 되고 실제 전진하는지 후진하는지 자체도 테스트를 할 수가 없다는 단점이 있습니다.

```jsx
public interface MovingNumber {
    int moveNumber();
}
```

```jsx
public class Car {
    private final MovingNumber movingNumber;

    public Car(final MovingNumber movingNumber) {
        this.movingNumber = movingNumber;
    }
}
```

```jsx
public class Game {
    public static void main(final String[] args) {
        final Car car = new Car(() -> (int)Math.random() * 10);
    }
}
```

- 위와 같이 interface로 추출해 메서드를 만들어 실제 구현체에서 람다로 구현하는 방식으로 한다면 실제 테스트 코드에서 아래와 같이 작성한다면 값을 제어할 수 있는걸 볼수 있습니다.

```jsx
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.Test;

class CarTest {
    @Test
    void constructorTest() {
        Car car = new Car(() -> 4);
    }
    
}
```

## 결론

- OCP를 적용한 코드는 실제로도 많이 볼 수 있습니다. DB Connection 같은 경우도 DB에 따라 다른 구현체를 가지고 있게 되어 DB 변경이 용이하게 되기도 합니다.
- 또한 전략패턴을 사용하게 된다면 테스트를 하기 어려운 부분도 용이하게 할 수 있는 것을 볼 수 있습니다.

## 참고자료

[[10분 테코톡] 👾베디의 OCP와 전략패턴](https://www.youtube.com/watch?v=90ZDvHl8ROE&t=503s)
