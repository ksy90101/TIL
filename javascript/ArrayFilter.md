
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

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d930a530-c079-4378-bb28-76d14fd94b8d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d930a530-c079-4378-bb28-76d14fd94b8d/Untitled.png)

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

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd511825-3e0e-432a-8a9e-ee7ca5b56039/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd511825-3e0e-432a-8a9e-ee7ca5b56039/Untitled.png)

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

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26ec8912-d0a3-4676-b26a-1cf0cc31874c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26ec8912-d0a3-4676-b26a-1cf0cc31874c/Untitled.png)