# 2장. 타락한 객체

## 일반적인 주기

1. 테스트 작성한다.
2. 실행 가능하게 만든다.
3. 올바르게 리팩토링 한다.

- 이 주기의 목적은 작동하는 깔끔한 코드를 만드는 것이;다.
- 두개를 같이 하는 건 어려운 일이다. 따라서 작동하는 코드를 먼저 해결하고 깔끔한 코드를 해결하는 방한으로 가면 된다.

## 새로운 요구사항

- Dollar에 대한 연산을 수행한 후에 해당 Dollar의 값이 바뀌는 점이다.
- 즉, 5달러를 2배로 하고 다시 5달러를 3배로 해서 10, 15가 나오길 원하는 것이다.

### 테스트 코드

```jsx
import {Dollar} from "../../src/domain/Dollar";

test('곱셈 테스트', () => {
    const dollar = new Dollar(5);

    dollar.times(2);

    expect(dollar.amount).toBe(10);

    dollar.times(3);

    expect(dollar.amount).toBe(15);
});
```

- 그러나 이전 코드는 두번째 expect에서 30이라고 나오면서 실패할 것이다.

## 해결해보자.

### 이전 코드

```jsx
export class Dollar{

    amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    times(multiplier: number){
        this.amount *= multiplier;
    }
}
```

### 동작하는 코드

- 그렇다면 새로운 객체를 반환해주면 되는거 아닐까?

```jsx
export class Dollar{

    amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    times(multiplier: number){
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }
}
```

```jsx
import {Dollar} from "../../src/domain/Dollar";

test('곱셈 테스트', () => {
    const dollar = new Dollar(5);

    let multiplyDollar = dollar.times(2);

    expect(multiplyDollar.amount).toBe(10);

    multiplyDollar = dollar.times(3);

    expect(multiplyDollar.amount).toBe(15);
});
```

- 위와 같이하면 테스트 코드를 통과할 것이다.

## 동작하는 테스트를 만드는 2가지 방법

### 가짜로 구현하기

- 상수를 반환하게 만들고 진짜 코드를 얻을 때까지 단계적으로 상수를 변수로 바꾸어 간다.

### 명백한 구현 사용하기

- 실제 구현을 입력한다.

### 둘중에 어떤걸로?

- 두 방법을 번갈아 가면서 사용하면 된다.

## 지금까지 정리

- 설계상의 결함을 그 결함으로 인해 실패하는 테스트로 변환했다.
- 스텁 구현으로 빠르게 컴파일을 통과하도록 만들었다.
- 올바르다고 생각하는 코드를 입력하여 테스트를 통과했다.