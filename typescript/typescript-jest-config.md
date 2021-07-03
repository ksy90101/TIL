# TypeScript를 이용해 Jest 테스트를 진행해보자.

## 서론

- 간단한 node 빈 프로젝트가 있다고 합시다. (package.json만 있는 프로젝트)
- Jest는 TypeScript를 지원하는 단위 테스트 도구이기 떄문에 사용하기 좋습니다.

## 필요한 모듈 설치

```java
npm i jest @types/jest ts-jest typescript -D
```

1. jest

    단위 테스트 프레임워크

2. @types/jest
    - jest types
3. ts-jest
    - jest의 ts 모듈
4. typescript
    - 타입 스크립트 모듈

## Jest 설정

- jest.config.js를 만들어 아래와 같이 설정한다.

```jsx
module.exports = {
  "testMatch": [
    "**/__tests__/**/*.+(ts|tsx|js)",
    "**/?(*.)+(spec|test).+(ts|tsx|js)"
  ],
  "transform": {
    "^.+\\.(ts|tsx)$": "ts-jest"
  },
}
```

- 간단하게 ts인 경우에는 ts-jest를 사용하겠다는 의미고, test 파일명 정의라고 생각하면 됩니다.

## 테스트 코드 작성

```tsx
import {Dollar} from "../../src/domain/Dollar";

test('곱셈 테스트', () => {
    const dollar = new Dollar(5);

    dollar.times(2);

    expect(dollar.amount).toBe(10);
});
```

```tsx
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

- 간단하게 금액을 2배로 곱셈하는 테스트를 작성해보려고 합니다.
- `npm jest` 를 이용하면 쉽게 테스트를 할수 있는걸 알 수 있습니다.

## 출처

[Jest](https://basarat.gitbook.io/typescript/intro-1/jest)
