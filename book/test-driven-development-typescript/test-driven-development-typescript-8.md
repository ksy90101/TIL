## 이번에 할 일

- Dollar와 Franc의 중복 코드 제거
- 현재 보면 times() 메서드 코드가 거의 똑같다.
- 그 전에 코드를 한번 살펴보자.

## 이전 코드

```tsx
import {Money} from "./Money";

export class Dollar extends Money {

    constructor(amount: number) {
        super(amount);
    }

    public times(multiplier: number) {
        const multiplyDollar = this.amount * multiplier;
        return new Dollar(multiplyDollar);
    }
}
```

```tsx
import {Money} from "./Money";

export class Franc extends Money{

    constructor(amount: number) {
        super(amount);
    }

    public times(multiplier: number) {
        const multiplyFranc = this.amount * multiplier;
        return new Franc(multiplyFranc);
    }
}
```

- 위의 중복코드를 없애기 위해 어떻게 해야 할까? 바로 상위 클래스로 올리는 방법이다.
- 테스트 코드를 봐보자.

```tsx
import {Dollar} from "../../../src/8장/domain/Dollar";
import {Franc} from "../../../src/8장/domain/Franc";

describe("Money times method Test", () => {
    test('dollar multiplication Test', () => {
        const dollar = new Dollar(5);
        expect(dollar.times(2)).toEqual(new Dollar(10));
        expect(dollar.times(3)).toEqual(new Dollar(15));
    });

    test('franc multiplication Test', () => {
        const franc = new Franc(5);
        expect(franc.times(2)).toEqual(new Franc(10));
        expect(franc.times(3)).toEqual(new Franc(15));
    });
})
```

- 이러한 코드를 리팩토링 하기 위해서는 팩토리 메서드를 사용하는 것이 좋을 수도 있다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export abstract class Money {
    amount: number;

    protected constructor(amount: number) {
        this.amount = amount;
    }

    static dollar(amount: number): Franc {
        return new Dollar(amount)
    }

    static franc(amount: number): Franc {
        return new Franc(amount)
    }
}
```

```tsx
import {Dollar} from "../../../src/8장/domain/Dollar";
import {Franc} from "../../../src/8장/domain/Franc";
import { Money } from "../../../src/8장/domain/Money";

describe("Money times method Test", () => {
    test('dollar multiplication Test', () => {
        const dollar: Money = Money.dollar(5);
        expect(dollar.times(2)).toEqual(new Dollar(10));
        expect(dollar.times(3)).toEqual(new Dollar(15));
    });

    test('franc multiplication Test', () => {
        const franc: Money = Money.franc(5);
        expect(franc.times(2)).toEqual(new Franc(10));
        expect(franc.times(3)).toEqual(new Franc(15));
    });
})
```

- Money라고 하면 times() 메서드가 선언되지 않았다고 한다.
- 이럴때 추상 클래스로 선언해서 처리하는 것이 더 좋다.

```tsx
import {Franc} from "./Franc";
import {Dollar} from "./Dollar";

export abstract class Money {
    amount: number;

    protected constructor(amount: number) {
        this.amount = amount;
    }

    static dollar(amount: number): Franc {
        return new Dollar(amount)
    }

    static franc(amount: number): Franc {
        return new Franc(amount)
    }

    abstract times(multiplier: Money) : Money;
}
```

- 이후에 다시 테스트를 진행하면  성공하는 것을 볼 수 있다.
- 좀 더 좋은 방법은 times를 구현한 곳에서 Money를 반환하게 만드는 것이다.
- 하위 클래스에 대한 직접적인 참조가 적어진다면 하위 클래스를 제거하기 위해 한발 짝 다가갈 수 있다.

## 지금까지 한 일

- 동일한 메서드의 두 변이형 메서드 서명부를 통일시킴으로써 중복 제거를 향해 한 단계 더 전진했다.
- 최소한 메서드 선언부만이라도 공통 상위 클래스로 옮겼다.
- 팩토리 메서드 도입하여 테스트 코드에서 콘크리트 하위 클래스의 존재 사실을 분리했다.
- 하위 클래스가 사라지면 몇몇 테스트는 불필요한 여분의 것이 된다는 것을 인식했다.