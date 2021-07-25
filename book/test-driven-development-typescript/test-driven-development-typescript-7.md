# 6장. 돌아온 '모두를 위한 평등'

## 5장의 테스트

- 5장의 테스트는 빨리 통과하기 위해 코드를 복사해 붙여넣는 죄를 저질렀다.
- 그렇다면 중복코드를 제거하기 위해 좋은 방법은 바로 상속을 사용하는 것이다.
- 두 클래스의 공통 상위 클래스 Money를 만드는 것이다.

## 객체 재 정리

```kotlin
class Money {
    protected amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }
}
```

```kotlin
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number) {
        super(amount);
    }

    public times(multiplier: number) {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }

    public equals(object: Money) {
        return this.amount === object.amount;
    }
}
```

```kotlin
import {Money} from "./Money";

export class Franc extends Money{

    constructor(amount: number) {
        super(amount);
    }

    public times(multiplier: number) {
        const multiplyFranc = this.amount * multiplier;
        return new Money(multiplyFranc);
    }

    public equals(object: Money) {
        return this.amount === object.amount;
    }
}
```

## 테스트 케이스

```kotlin
import {Dollar} from "../../../src/6장/domain/Dollar";

test('multiplication Test', () => {
    const dollar = new Dollar(5);
    expect(dollar.times(2)).toEqual(new Dollar(10));
});
```

```kotlin
import {Franc} from "../../../src/6장/domain/Franc";

test('multiplication Test', () => {
    const franc = new Franc(5);
    expect(franc.times(2)).toEqual(new Franc(10));
});
```

- 아직 중복코드 중 equals()가 남아있다.

## 중복코드 제거

```typescript
export class Money {
    amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    public equals(object: Money) {
        return this.amount === object.amount;
    }
}
```

```typescript
import {Dollar} from "../../../src/6장/domain/Dollar";

test('equal method Test', () => {
    const dollar1 = new Dollar(5);
    const dollar2 = new Dollar(5);

    expect(dollar1.equals(dollar2)).toBeTruthy()
});
```

```typescript
import {Franc} from "../../../src/6장/domain/Franc";

test('equal method Test', () => {
    const dollar1 = new Franc(5);
    const dollar2 = new Franc(5);

    expect(dollar1.equals(dollar2)).toBeTruthy()
});
```

## 결론

- 있으면 좋을 것 같은 테스트를 작성하라. 그렇지 하지 않으면 결국에는 리팩토링하다가 뭔가 깨트릴 것이다. 리팩토링에 대한 안 좋은 느낌을 갖게 되고, 리팩토링을 덜 하게 된다.

## 지금까지 한 것

- 공통된 코드를 첫 번째 클래스에서 상위 클래스로 단계적으로 옮겼다.
- 두 번째 클래스도 하위 클래스로 만들었다.
- 불필요한 구현을 제거하기 전에 두 equals() 구현을 일치시켰다.