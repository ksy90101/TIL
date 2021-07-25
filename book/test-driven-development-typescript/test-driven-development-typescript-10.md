# 10장. 흥미로운 시간

## 해야할 일

- 공용 times 만들기
- 보면 times()가 비슷해 보인다. 그러다 동일하다고 할 수 없다. 동일하게 만들어볼 방법이 있을까?
- 일단 테스트 코드부터 작성해보자

## 테스트 코드

```tsx
import {Money} from "../../../src/10장/domain/Money";

test('dollar multiplication Test', () => {
    const dollar: Money = Money.dollar(5);

    expect(dollar.times(2)).toStrictEqual(Money.dollar(10));
    expect(dollar.times(3)).toStrictEqual(Money.dollar(15));
});
```

```tsx
import {Money} from "../../../src/10장/domain/Money";

test('franc multiplication Test', () => {
    const franc: Money = Money.franc(5);
    expect(franc.times(2)).toStrictEqual(Money.franc(10));
    expect(franc.times(3)).toStrictEqual(Money.franc(15));
});
```

- 일단 반환값이 비슷해야 할듯 아다 지금은 팩토리 메서드로 각 객체를 반환한다. 그걸 Money로 바꿔보자.

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number, currency: string) {
        super(amount, currency);
    }

    public times(multiplier: number): Money {
        const multiplyDollar = this.amount * multiplier;
        return new Money(multiplyDollar, "USD");
    }

    public getCurrency(): String {
        return this.currency
    }
}
```

```tsx
import {Money} from "./Money";

export class Franc extends Money {
    constructor(amount: number, curreny: string) {
        super(amount, curreny);
    }

    public times(multiplier: number): Money {
        const multiplyDollar = this.amount * multiplier;
        return new Money(multiplyDollar, "CHF");
    }

    public getCurrency(): string {
        return this.currency;
    }
}
```

- 근데 컴파일 에러가 발생한다. 추상 클래스가 아닌 일반 클래스로 변경하라고 한다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export class Money {
    amount: number
    currency: string

    protected constructor(amount: number, currency: string) {
        this.amount = amount
        this.currency = currency
    }

    static dollar(amount: number): Money {
        return new Dollar(amount, "USD")
    }

    static franc(amount: number): Money {
        return new Franc(amount, "CHF")
    }

    times(multiplier: number): Money{
        return null;
    }

    getCurrency(): String{
        return null;
    }
}
```

- 변경해보자. 그럼 통과할것이다. 근데 null을 반환하고 있다. 벌써 2개의 추상 클래스를 위로 올릴 수 잇을 것이다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export class Money {
    amount: number
    currency: string

    protected constructor(amount: number, currency: string) {
        this.amount = amount
        this.currency = currency
    }

    static dollar(amount: number): Money {
        return new Dollar(amount, "USD")
    }

    static franc(amount: number): Money {
        return new Franc(amount, "CHF")
    }

    public times(multiplier: number): Money {
        const multiplyDollar = this.amount * multiplier;
        return new Money(multiplyDollar, this.currency);
    }

    public getCurrency(): String {
        return this.currency
    }
}
```

- 이제 Dollar와 Franc은 완전 간단해졌다.,

## 지금까지 한 일

- 두 times()를 일치시키기 위해 그 메서드들이 호출하는 다른 메서드들을 인라인시킨 후 상수를 변수로 바꿔주었다.
- Franc 대신 Money를 반환하는 변경을 시도한 뒤 그것이 잘 작동할지를 테스트가 말하도록 했다.