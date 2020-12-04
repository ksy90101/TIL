# JavaScript 2차원 배열

## 소개

- 자바스크립트는 진정한 2차원 배열이라는 것이 존재하지 않는다.
- Java처럼 `[][]` 이런식으로 선언이 불가능하다는 의미이다.
- 그러나 간단한 트릭을 이용하여 2차원 배열과 비슷한 배열을 만들수는 있다.

## 반복문을 사용

```jsx
let arr = new Array(5);
for(let i = 0; i < arr.length; i++) {
    arr[i] = new Array(5);
}
```

![making-2-dimensional-array-1](https://github.com/ksy90101/TIL/blob/master/javascript/img/making-2-dimensional-array-1.png?raw=true)

## ES6 버전

```jsx
const arr1 = Array.from(Array(5), () => new Array(5).fill(0));
```

![making-2-dimensional-array-2](https://github.com/ksy90101/TIL/blob/master/javascript/img/making-2-dimensional-array-2.png?raw=true)

### Array.from()

- 유사 배열 객체나 반복 가능한 객체를 얕은 복사로 객체를 만드는 함수

### Array.fill()

- 배열 안에 값을 채워 넣는 함수
