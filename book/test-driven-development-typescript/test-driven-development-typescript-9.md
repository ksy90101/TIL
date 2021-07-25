# 9장. 우리가 사는 시간

## 할 일

- 통화개념을 도입하기

## 통화를 표한기 위한 방법은?

- 복잡한 객체들을 원할 수도 있지만, 객체들이 필요한 만큼만 만들어 지도록 하기 위해 경량 팩토리를 사용할 수 있다. 그러나 지금은 문자열로 사용해보자.

## 테스트 코드

```tsx
import {Money} from "../../../src/9장/domain/Money";

test('currency Test', () => {
    expect("USD").toStrictEqual(Money.dollar(10).currency());
    expect("CHF").toStrictEqual(Money.franc(15).currency());
});
```

## 구현

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export abstract class Money {
    amount: number

    protected constructor(amount: number) {
        this.amount = amount
    }

    static dollar(amount: number): Money {
        return new Dollar(amount)
    }

    static franc(amount: number): Money {
        return new Franc(amount)
    }

    abstract times(multiplier: number) : Money;

    abstract currency(): String;
}
```

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number) {
        super(amount);
    }

    public times(multiplier: number): Dollar {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }

    currency(): String {
        return "USD";
    }
}
```

```tsx
import {Money} from "./Money";

export class Franc extends Money {
    constructor(amount: number) {
        super(amount);
    }

    public times(multiplier: number): Franc {
        const multiplyDollar = this.amount * multiplier;
        return new Franc(multiplyDollar);
    }

    currency(): String {
        return "CHF";
    }
}
```

- 리팩토링을 해본다면 변수에 저장해 메서드에서 그걸 반환하게 만들 수 잇을 것이다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export abstract class Money {
    amount: number

    protected constructor(amount: number) {
        this.amount = amount
    }

    static dollar(amount: number): Money {
        return new Dollar(amount)
    }

    static franc(amount: number): Money {
        return new Franc(amount)
    }

    abstract times(multiplier: number) : Money;

    abstract getCurrency(): String;
}
```

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    currency: string

    constructor(amount: number) {
        super(amount);
        this.currency = "USD"
    }

    public times(multiplier: number): Dollar {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }

    public getCurrency(): String {
        return this.currency
    }
}
```

```tsx
import {Money} from "./Money";

export class Franc extends Money {
    currency: string

    constructor(amount: number) {
        super(amount);
        this.currency = "CHF"
    }

    public times(multiplier: number): Franc {
        const multiplyDollar = this.amount * multiplier;
        return new Franc(multiplyDollar);
    }

    public getCurrency(): string {
        return this.currency;
    }
}
```

- 우린 천천히 하나하나씩 하고 있다는걸 기억하고 있어라, Dollar와 Franc에서 모두 통화가 필요하기 때문에 상위 클래스로 올릴수 있을 것이다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export abstract class Money {
    amount: number
    currency: string

    protected constructor(amount: number, currency: string) {
        this.amount = amount
        this.currency = currency
    }

    static dollar(amount: number): Money {
        return new Dollar(amount, null)
    }

    static franc(amount: number): Money {
        return new Franc(amount, null)
    }

    abstract times(multiplier: number): Money;

    abstract getCurrency(): String;
}
```

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number, currency: string) {
        super(amount, "USD");
    }

    public times(multiplier: number): Dollar {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar, null);
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
        super(amount, "CHF");
    }

    public times(multiplier: number): Franc {
        const multiplyDollar = this.amount * multiplier;
        return new Franc(multiplyDollar, null);
    }

    public getCurrency(): string {
        return this.currency;
    }
}
```

- 인자로 받는 이유는 같은 생성자를 사용하기 위함이라고 생각하자.
- 이렇다면 팩토리 메서드에서 인자로 전해주면 되지 않을까? 더 깔끔해질거 같다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export abstract class Money {
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

    abstract times(multiplier: number): Money;

    abstract getCurrency(): String;
}
```

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number, currency: string) {
        super(amount, currency);
    }

    public times(multiplier: number): Dollar {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar, null);
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

    public times(multiplier: number): Franc {
        const multiplyDollar = this.amount * multiplier;
        return new Franc(multiplyDollar, null);
    }

    public getCurrency(): string {
        return this.currency;
    }
}
```

- 여기서 한번 생각해보자 왜 times에서 팩토리 메서드를 쓰지 않는가?

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number, currency: string) {
        super(amount, currency);
    }

    public times(multiplier: number): Money {
        const multiplyDollar = this.amount * multiplier;
        return Money.dollar(multiplyDollar);
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
        return Money.franc(multiplyDollar);
    }

    public getCurrency(): string {
        return this.currency;
    }
}
```

- 지금 굉장히 TDD를 작은 단위로 하는 것 같냐? 사실 성큼성큼 걷는 것 보다 보폭을 줄이는것이 좋다.

## 지금까지 한일

- 큰 설계 아이디어를 다루다가 조금 곤경에 빠졌다. 그래서 좀 전에 주목했던 더 작은 작업을 수행했다.
- 다른 부분들을 호출자(팩토리 메서드)로 옮김으로써 두 생성자를 일치시켰다.
- times()가 팩토리 메서드를 사용하도록 만들기 위해 리팩토링을 잠시 중단했다.
- 비슷한 리팩토링을 한번의 큰 단계로 처리했다.
- 동일한 생성자들을 상위 클래스로 올렸다.