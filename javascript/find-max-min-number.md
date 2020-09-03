# 배열 내의 최대값 최소값 찾기(Math.min.apply(), Math.max.apply())

## [Math.min()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Math/min)

- 인자로 들어온 값 중에서 가장 작은 값을 반환하는 함수

```jsx
Math.min(10, 20); // 10
```

## [Math.max()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Math/max)

- 인자로 들어온 값 중에서 가장 작은 값을 반환하는 함수

```jsx
Math.max(10,20); // 20
```

## [Function.prototype.apply()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

- 함수가 실행될 때 내부 콘텍스트의 this는 객체 자신 또는 window 객체를 가르킨다.
- 이때 this가 가리키는 대상 바꾸는 방법 중 하나입니다.
- apply()는 인자로 배열(또는 유사 배열 객체)로 제공되는 arguments로 함수를 호출한다.

## 최소값과 최대값 찾기

```jsx
const arr = [4,3,2,1];

const minNumber = Math.min.apply(null, arr); // 1
const maxNumber = Math.max.apply(null, arr); // 4
```

- `null` 을 첫번째 인자로 사용하면 context는 window 객체가 된다.
- 위와 같이 사용하면 두번째 인자의 배열의 값을 순서대로 호출한 메서드의 인자로 보내 실행한다.

## 주의사항

- 배열 내의 비교 불가능한 값이 있다면 NaN이 나온다. 
- 대부분의 경우에는 Math 객체를 사용하면 되지만 큰 배열(~$10^7$) 정도 되면 `RangeError: Maximum call stack size exceeded` 가 나오니 주의하자.

## 참고자료

[[JavaScript] 배열 요소 중 최대값 최소값 찾기](https://programmingsummaries.tistory.com/108)
