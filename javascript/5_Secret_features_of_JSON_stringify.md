# 5 Secret features of JSON.stringify() (JSON.stringify()의 5가지 비밀 기능)

JavaScript 개발자들은 `JSON.stringify()` 메서드로 대부분 디버깅에 사용합니다. 그러나 `console.log()` 가 있어도 무슨 소용이 있을까요?

```jsx
// 사용자 객체 초기화

const user = {
 “name” : “Prateek Singh”,
 “age” : 26
}

console.log(user); // [object Object]
```

`console.log` 로는 원하는 결과를 인쇄하는 대 도움되지 않습니다. `[object Object]` 란 객체에서 문자열로의 기본 변환이기 때문에 이렇게 출력됩니다. 그래서 우리는 `JSON.stringify()`  를 사용해 변환한 다음 콘솔에서 인쇄해야 합니다.

```jsx
const user = {
 “name” : “Prateek Singh”,
 “age” : 26
}

console.log(JSON.stringify(user)); // "{ "name" : "Prateek Singh", "age" : 26 }"
```

일반적으로 위와 같이 간단한 방법으로 이 기능을 사용합니다. 그러나 이 부분은 숨겨진 비밀이 있으며, 개발하는데 편리함을 줄 수 있습니다.

## **1. 두 번째 인수 (Array)**

`stringify()` 메서드는 두 번째 인수도 가질 수 있습니다. 바로 콘솔에서 인쇄하려는 객체의 키 배열입니다. 대상 제품이 있고 제품 이름을 알고 싶다면 아래와 같이 하면 가능합니다.

```json
{“id”:”0001",”type”:”donut”,”name”:”Cake”,”ppu”:0.55,”batters”:{“batter”:[{“id”:”1001",”type”:”Regular”},{“id”:”1002",”type”:”Chocolate”},{“id”:”1003",”type”:”Blueberry”},{“id”:”1004",”type”:”Devil’s Food”}]},”topping”:[{“id”:”5001",”type”:”None”},{“id”:”5002",”type”:”Glazed”},{“id”:”5005",”type”:”Sugar”},{“id”:”5007",”type”:”Powdered Sugar”},{“id”:”5006",”type”:”Chocolate with Sprinkles”},{“id”:”5003",”type”:”Chocolate”},{“id”:”5004",”type”:”Maple”}]}
```

그러나 너무 쓸데없는 결과들이 많이 나와서 키를 찾기가 어렵습니다. 객체가 거대해질 수록 찾는게 더 어려워집니다. 이 때, 두 번째 인수에 키 배열을 넣으면 해당 키의 값만 나오게 됩니다.

```jsx
console.log(JSON.stringify(product,[‘name’]); // {"name" : "Cake"}
```

 번째 인수의 키 배열을 전달해 필요한 키만 출력할 수 있습니다.

## **2. 두 번째 인수 (Function)**

두 번째 인수에 함수를 전달할 수 있으며, 함수에 작성된 로직으로 key-value를 체크해 인쇄합니다.  `undefined` 인 경우에는 표시되지 않습니다.

```jsx
const user = {
 “name” : “Prateek Singh”,
 “age” : 26
}

JSON.stringify(user, (key, value) => {
	if (typeof value === 'string') {
		return undefined;
	}
	return value;
}); // { "age" : 26 }
```

위의 코드는 값이 문자열인 경우에만 반환됩니다.

## **3: Number로서의 세번째 인수**

세 번째 인수는 문자열의 간격을 제어하며,  숫자를 넣으면 숫자로 들여쓰기가 됩니다.

```
JSON.stringify(user, null, 2);

//{
//  "name": "Prateek Singh",
//  "age": 26,
//  "country": "India"
//}
```

## **4. String의 세 번째 인수**

세 번째에 문자열을 넣으면 공백 문자 대신 인수로 넣은 문자열로 들여쓰기를 나타냅니다.

```
JSON.stringify(user, null,'**');

{
  **"name": "Prateek Singh",
  **"age": 26,
  **"country": "India"
}

```

## **5. toJSON method**

`toJson` 의 속성으로 객체의 일부를 나타낼 수 잇는 메서드가 있습니다. `JSON.stringify` 를 사용하면 해당 메서드를 반환합니다.

 

```jsx
const user = {
 firstName : "Prateek",
 lastName : "Singh",
 age : 26,
 toJSON() {
    return {
      fullName: `${this.firstName} + ${this.lastName}` 
	};
}

console.log(JSON.stringify(user)); // "{ "fullName" : "Prateek Singh"}"
```
