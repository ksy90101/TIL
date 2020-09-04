# 배열 내 특정 값 삭제하기

## [Array.prototype.indexOf()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)

- 배열 내의 특정 값의 인덱스를 찾는 메서드로 해당 값이 존재하지 않으면 `-1`을 반환합니다.
- 여기서 특징은 엄격한 동등성(`===`)을 사용하여 값을 찾아냅니다.
- 예를들어 배열에 "1"이라는 문자열이 있는데 1이라는 숫자형을 찾으려고 하면 찾을수가 없습니다.

```jsx
const arr = [4,3,2,1];
arr.indexOf(4) // 1
```

## [Array.prototype.splice()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

- 배열의 기존 요소를 삭제,변경 또는 새 요소를 추가하여 배열 내용을 변경합니다.

```jsx
const arr = [4,3,2,1];
arr.splice(1, 1); // [3]
console.log(arr); // [4, 2, 1]
```

- 첫번째 인자는 배열 변경을 시작할 인덱스이며 두번째 인자는 배열에서 변경할 요소의 수입니다.

## 배열 내 특정 값 삭제하기

```jsx
const arr = [4,3,2,1]
const smallNumber = Math.min.apply(null, arr);
const smallNumberIndex = arr.indexOf(smallNumber); // 3

arr.splice(smallNumberIndex, 1) // [1]

console.log(arr); // [4,3,2]
```
