# 3장. 모두를 위한 평등

## Value Object

- 지금까지 했던 Dollar객체는 값처럼 쓰는 Value Object 패턴이다.
- 제약사항이 있는데, 객체의 인스턴스 벼눗가 생성자를 통해서 일단 설정된 후에는 결코 변하지 않는 다는 것이다.
- 따라서 새로운 객체를 내보내 줬다.

## 새로운 요구사항

- 과연 5달러와 5달러 각 다른 객체가 동일할까?
- 테스트 코드를 작성해보자.

## 동치성 테스트

```jsx
import {Dollar} from "../../../src/4장/domain/Dollar";

test('equals Test', () => {
    const dollar1 = new Dollar(5);
    const dollar2 = new Dollar(5);

    expect(dollar1).toEqual(dollar2);
});
```

```jsx
export class Dollar{

    amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    public times(multiplier: number) {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }
}
```

- 사실 TypeScript는 기본적으로 equals()메서드가 있지 않다. toEqual을 사용하면 기본적으로 값들을 비교해서 테스트를 통과시켜준다.

### 객체지향의 동치성을 위해 equals()를 구현해보자.

```jsx
import {Dollar} from "../../../src/4장/domain/Dollar";

test('equals Test', () => {
    const dollar1 = new Dollar(5);
    const dollar2 = new Dollar(5);

    expect(dollar1.equals(dollar2)).toBeTruthy();
});
```

```jsx
export class Dollar{

    amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    public times(multiplier: number) {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }

    public equals(object: Object) {
        return true
    }
}
```

- 이렇게 하면 결국 테스트 통과를 위한 코드만 작성해놓은 편이다. 리팩토링을 해보자.

```jsx
export class Dollar{

    amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    public times(multiplier: number) {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }

    public equals(object: Dollar) {
        return this.amount === object.amount;
    }
}
```

## 아직 남은건?

- null처리와 다른 Object가 들어왔을때를 처리하지는 않았지만, 차후 처리해보자.

## 번외

### toBe()

- 기본값이나 Object의 참조 ID를 체크할 수 있습니다.

### toEqual()

- 재귀적으로 모든 속성을 비교합니다.

## 정리

- 우리의 디자인 패턴(값 객체)이 하나의 또 다른 오퍼레이션을 암시한다.
- 해당 오퍼레이션을 테스트 했다.
- 해당 오퍼레이션을 간단히 구현했다.
- 곧장 리팩토링 하는 대신 테스트를 조금 더 했다.
- 두 경우를 모두 수용할 수 있도록 리팩토링했다.