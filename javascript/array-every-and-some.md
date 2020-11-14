# Array.every() & Array.some()

## Array.every()

### every() 함수란?

- 특정 조건이 배열의 모든 요소와 부함하는지 확인하는 함수

```jsx
array.every(callback(element[, index[, array]])[, thisArg])
```

- callback : 함수
    - element : 배열 요소 중 처리되고 있는 요소
    - index : 배열 요소 중 처리되고 있는 인덱스 (Option)
    - array : 배열 (Option)
- thisArg : callback이 실행될 때 this 값으로 사용

### 예제

- 배열 내 모든 요소가 홀수일 경우 true를 반환

```jsx
const array = [1, 3, 5];
console.log(array.every((element) => element % 2 === 1));
```

## Array.some()

### some() 함수란?

- 특정 조건이 배열의 요소 중 하나라도 맞는 지를 확인하는 함수

```jsx
array.some(callback(element[, index[, array]])[, thisArg])
```

- callback : 함수
    - element : 배열 요소 중 처리되고 있는 요소
    - index : 배열 요소 중 처리되고 있는 인덱스 (Option)
    - array : 배열 (Option)
- thisArg : callback이 실행될 때 this 값으로 사용

### 예제

- 배열 내 요소 중에 하나라도 홀수가 존재할 경우 true를 반환하는 로직

```jsx
const array = [1, 2, 3, 4, 5];
console.log(array.some((element) => element % 2 ===1));
```
