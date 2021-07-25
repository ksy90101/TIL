# 11장. 모든 악의 근원

## 해야 할 일

- Dollar / Franc 중복
- Dollar와 Franc은 생성자밖게 없다. 삭제해도 되지 않을까? 지금 같은 경우에는

## 테스트 코드

```tsx
import {Money} from "../../../src/11장/domain/Money";

test('dollar multiplication Test', () => {
    const dollar: Money = Money.dollar(5);

    expect(dollar.times(2)).toStrictEqual(Money.dollar(10));
    expect(dollar.times(3)).toStrictEqual(Money.dollar(15));
});
```

```tsx
import {Money} from "../../../src/11장/domain/Money";

test('franc multiplication Test', () => {
    const franc: Money = Money.franc(5);
    expect(franc.times(2)).toStrictEqual(Money.franc(10));
    expect(franc.times(3)).toStrictEqual(Money.franc(15));
});
```

- 삭제하면 테스트 코드는 빨간줄이 들것이다. 그렇다면 어떻게 해야 할까? 바로 Money로 모두 변경하고 각 차이는 메서드를 통하면 될것 같다.

```tsx
export class Money {
    amount: number
    currency: string

    protected constructor(amount: number, currency: string) {
        this.amount = amount
        this.currency = currency
    }

    static dollar(amount: number): Money {
        return new Money(amount, "USD")
    }

    static franc(amount: number): Money {
        return new Money(amount, "CHF")
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

- 하나의 객체로 관리할 수 있게 되었다.

## 지금까지 한 일

- 하위 클래스의 속을 들어내는 걸 완료하고, 하위 클래스를 삭제했다.
- 기존의 소스 구조에서는 필요했지만 새로운 구조에서는 필요 없게 된 테스트를 제거했다.