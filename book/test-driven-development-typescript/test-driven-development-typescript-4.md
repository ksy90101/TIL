# 4장. 프라이버시

## 간단하게 리팩토링해보기

```kotlin
import {Dollar} from "../../../src/4장/domain/Dollar";

test('multiplication Test', () => {
    const dollar1 = new Dollar(5);
    const dollar2 = dollar1.times(2);
    const dollar3 = new Dollar(10);
    expect(dollar2).toEqual(dollar3);
});
```

- 이걸 아래와 같이 변경할 수 있을 것이다.

```kotlin
import {Dollar} from "../../../src/4장/domain/Dollar";

test('multiplication Test', () => {
    const dollar1 = new Dollar(5);
    expect(dollar1.times(2)).toEqual(new Dollar(10));
});
```

- 이렇게 한다면 amount를 사용하는 곳은 자기 자신밖게 없다.
- 따라서 접근 제어자를 private으로 만들자

```kotlin
export class Dollar {

    private readonly amount: number;

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

- 그러나 여기서 문제가 있다. 동치성 테스트가 동치성에 대한 코드가 정확히 작동한다는 것에 검증하는데 실패한다면 위의 테스트도 실패할 것이다.
- 따라서 계속해서 초록 막대 아래에서 대담하게 앞으로 나아갈수 있도록 용감하게 리팩토링을 해야 한다.

## 지금까지 배운건?

- 오직 테스트를 향상시키기 위해서만 개발된 기능을 사용했다.
- 두 테스트가 동시에 실패하면 망한다는 점을 인식했다.
- 위험 요소가 있음에도 계속 진행했다.
- 테스트와 코드 사이의 결합도를 낮추기 위해, 테스트하는 객체의 새기능을 사용했다.