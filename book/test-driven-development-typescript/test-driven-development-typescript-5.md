# 5장. 솔직히 말하자면

## 이러한 테스트는?

```kotlin
$5 + 10CHF = $10 (환율이 2:1)
```

- 일단 지금까지 만든 Dollar 말고 Franc을 표현할 수 있다는 객체가 필요할 것이다.

```kotlin
export class Franc {

    private readonly amount: number;

    constructor(amount: number) {
        this.amount = amount;
    }

    public times(multiplier: number) {
        const multiplyFranc = this.amount * multiplier;
        return new Franc(multiplyFranc);
    }

    public equals(object: Franc) {
        return this.amount === object.amount;
    }
}
```

```kotlin
import {Franc} from "../../../src/5장/domain/Franc";

test('multiplication Test', () => {
    const franc = new Franc(5);
    expect(franc.times(2)).toEqual(new Franc(10));
});
```

- 우리가 만든 Dollar를 Franc으로 변경하면 된다.

### 단계를 다시 생각해보자.

1. 테스트 작성
2. 컴파일되게 하기
3. 실패하는지 확인하기 위해 실행
4. 실행하게 만듦.
5. 중복 제거

- 이 단계에서 4단계까지는 빠르게 진행하는것이 중요하다.
- 지금은 단순히 컴파일되게 하기 단계일것이다.
- 현재 중복이 엄청나게 많을것이다.

## 지금까지 결심한것

1. 큰 테스트를 공략할 수 없다. 그래서 진전을 나타낼 수 있는 자그마한 테스트를 만들었다.
2. 뻔뻔스럽게도 중복을 만들고 조금 고쳐서 테스트를 작성했다.
3. 설상가상으로 모델 코드까지 도매금으로 복사하고 수정해서 테스트를 통과했다.
4. 중복이 사라지기 전에는 집에 가지 않겠다고 약속했다.