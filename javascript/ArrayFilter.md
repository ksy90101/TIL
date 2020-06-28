
- 함수 명칭과 같이 조건을 설정하여 조건에 맞는 값을 가지고 새로운 배열을 만드는 것입니다.
- 새로운 배열을 만드는 것이기 때문에 원래 배열을 전혀 변경하지 않는다.

### 함수 구문

> array.filter(callbackFunction(element, index, array), thisArg);

> array.filter((element, index, array) ⇒ 조건,  thisArg);

### 매개변수

- element : 배열에 있는 값
- Index : 배열 인덱스
- array : 사용되는 배열
- thisArg : filter에서 사용될 this 값(선택적으로 사용하지 않는다면 `undefined`)
- Callback 함수 실행 시 this로 사용되는 값 즉, 호출될 때 그 값은 callback의 this 값으로 전달됩니다.
- 그 이외에, undefined값도 callback의 this 값으로 쓰기 위해 전달됩니다.
- 결국 callback에 의해 관찰될 수 있는 this 값은 this를 결정하는 함수의 평소 규칙에 따라 결정됩니다.

### 예제

```javascript
const array = [1,2,3,4,5];

array.filter((element, index, array) => {
    console.log(element);
    console.log(index);
    console.log(array);
})
```

![arrayFilter-1](https://github.com/ksy90101/TIL/blob/master/javascript/img/arrayFilter-1.png)

### 응용 예제

- index가 짝수인 값만 나오게 해라.

    ```java
const array = [1,2,10,5,7,8,10,8,10];
array.filter((element, index) => index % 2 === 0);
```

- Json

```javascript
const json = [{name : "아이유", age: 28},
              {name : "이지은", age: 27},
              {name : "서예지", age: 25},
              {name : "김유정", age: 28}]

json.filter(({name, age}) => age === 28);
```

![arrayFilter-2](https://github.com/ksy90101/TIL/blob/master/javascript/img/arrayFilter-2.png)

- 아래와 같이 json 유효성 검사에도 사용이 가능하다.

```javascript
var arr = [
  { id: 15 },
  { id: -1 },
  { id: 0 },
  { id: 3 },
  { id: 12.2 },
  { },
  { id: null },
  { id: NaN },
  { id: 'undefined' }
];

arr.filter(({id}) => isNumber(id) && id !== 0);
```

![arrayFilter-3](https://github.com/ksy90101/TIL/blob/master/javascript/img/arrayFilter-3.png)

### 참고자료

- [https://ktpark1651.tistory.com/215](https://ktpark1651.tistory.com/215)
- [https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/filter](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
