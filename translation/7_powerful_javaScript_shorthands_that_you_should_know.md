# [7 Powerful JavaScript Shorthands That You Should Know(알아야 할 7개의 강력한 JavaScript의 단축 표기법)](https://medium.com/codex/7-powerful-javascript-shorthands-that-you-should-know-56a953e0c3b)

코딩 속도를 올리기 위해 사용할 수 있는 유용한 JavaScript의 단축 표기법입니다.

자바스크립트는 요즘 매우 강력한 프로그래밍 언어입니다. 자바스크립트로 우리는 할 수 있는 것들이 많이 있습니다. 그 외에도 자바스크립트에는 많은 유용한 도구, 프레임워크, 라이브러리를 발견할 수 있는 거대한 생태계가 있습니다.

자바스크립트는 개발자의 삶을 쉽게 만들어 주는 많은 강력한 기능과 도구들이 있습니다. 문법에는 자바스크립트 코드 빠르게 작성하거나 라인수를 줄이여서 사용할 수 있는 많은 단축 표기법들이 포함되어 있습니다. 특히 최신 ECMAScript 사용에 포함되어 있습니다.

이 글은 JavaScript에서 사용할 수 있는 단축 표기법들을 정리한 글입니다. 그럼 바로 들어가보겠습니다.

## **1. Convert string to a number**

일반적으로 문자를 숫자료 변환하기 위해 `parseInt()` 메서드를 사용합니다. 그러나 우리는 이거와 동일한 단축 표기법이 있습니다.

단항 연산자인 `+` 를 사용함으로써 우리는 문자를 숫자로 쉽게 변환할 수 있습니다.

Here is an example:

```jsx
const speed = "60";
console.log(typeof speed); //string
console.log(typeof +speed); //number
console.log(+speed); //60, not "60".
```

위와 같이 `+` 를 변수 시작 부분에 붙여 우리는 숫자로 변환할 수 있습니다.

숫자에서 문자로 변환하는 단축표기법이 있습니다. 숫자 타입의 변수에 빈 문자열만 추가해주면 됩니다.

```jsx
const speed = 100;

speed += ""; //returns "100" , not 100.

console.log(typeof speed); //string
```

여기서 주의해야할 점은 const에서는 해당 방법이 사용되지 않습니다. 그러나 `+` 로 문자를 숫자로 변환하는 문제는 가능합니다.

## **2. 삼항연산자 사용하기**

또 다른 단축 표기법은 삼항연산자 입니다. `?` 와 `:` 키워드를 사용해 짧게 조건문을 쉽게 작성할 수 있습니다.

`if-else` 를 사용하는 예제를 먼저 보겠습니다.

```jsx
const speed = 80;

if(speed < 30) {
 console.log('slow');
} else if(speed > 60) {
 console.log('fast');
} else {
 console.log('between 30 and 60');
}

//output: fast
```

위의 `if-else`문을 3항 연산자로 변경할 수 있습니다.

```jsx
const speed = 80;

console.log(speed < 30 ? 'slow': speed > 60 ? 'fast'
: 'between 30 & 60');

//output: fast
```

위와 같이 삼항 연산자를 사용해 작성할 수 있는 코드의 수를 쉽게 줄일 수 있습니다. 조건문을 작성하는데 2줄의 코드만이 필요했습니다. 그러나 조건이 많다면 위와 같이 사용하면 가독성이 떨어질수가 있습니다. 따라서 if문과 else문이 있을때만 사용하는 것이 좋습니다.

# **3. 단축평가값**

아래와 같이 `if문` 이 있다고 해봅시다.

```jsx
const isOnline = true;

if(isOnline) {
 console.log("Online");
}

//returns "online"
```

우리는 이걸 평가 값인 `&&` 를 사용해 쉽게 나타낼 수 있습니다.

```jsx
const isOnline = true;

isOnline && console.log("Online");

//returns "online"
```

단축 평가 값인 `&&` 은 조건문을 짧게 작성할 수 있는 방법중 하나입니다. 두 값 사이에 사용을 해 첫 번째 값이 true이면 두 번째 값을 반환하며, 그렇지 않으면 첫번째 값을 반환합니다.

단축평가값은 `||` 도 있습니다. 

```jsx
const me = "";

const you = me || {};
```

- me의 값을 가지고 있지 않은 경우에는 빈 객체를 생성하라는 의미입니다.

# **4. 배열 병합**

다차원 배열을 병합하는 가장 좋은 방법을 `flat()` 메서드를 사용하는 것입니다. 많은 개발자가 filter, concat 기타 등등 메서드들을 사용해 배열을 병합하는 것을 봤습니다. 아마 아직 flat 방법을 모르기 때문일겁니다.

이 메서드를 사용하면 한 줄의 코드로 배열을 병합할 수 있습니다. 이것은 하나의 옵션 인자(숫자)를 받습니다. 이 인자는 depth로 중첩 배열 구조를 평탄화할 때 사용할 깊이 값으로 기본값은 1입니다.

```jsx
let numbers = [9, [102, 5], [6, 3], 2];

numbers.flat(); 

//returns [9, 102, 5, 6, 3, 2]
```

## **5. 배열 병합 및 복사**

자바스크립트의 배열을 복사하거나 병합할때 spread operator인 `{...}` 을 사용하는 것이 가장 좋은 단축 표기법입니다.

병합 예제 : 

```jsx
const employees = ["Chris", "John"];
const entrepreneurs = ["James", "Anna"];

//merging both arrays to a new array.
const all =[...employees, ...entrepreneurs];console.log(all);

 //output: ["Chris", "John", "James", "Anna"]
```

복사 예제 : 

```jsx
const numbers = [4, 8, 9, 0];

//cloning the numbers array.
const clone = [...numbers];

console.log(clone); 
//output: [4, 8, 9, 0]
```

## **6. For문 단축 표기법**

대부분 for문을 사용해 배열을 반속하고 싶다면 아래와 같이 할 것입니다.

```jsx
const users = ["John", "Chris", "Mehdi", "James"];

for(let i = 0; i < users.length; i++){
   console.log(users[i]);
}

/*output:
   John
   Chris
   Mehdi
   James
*/
```

But we can achieve the same thing using the loop `for of` shorthand:

그러나 우리는 `for of` 를 이용해 동일한 결과를 얻을수도 있습니다.

```jsx
const users = ["John", "Chris", "Mehdi", "James"];

for (const user of users){
  console.log(user);
}

/*output:
   John
   Chris
   Mehdi
   James*/
```

추가적으로 `for in` 문법도 있습니다.

```jsx
const users = ["John", "Chris", "Mehdi", "James"];

for (const index in users){
  console.log(users[index]);
}

/*output:
   John
   Chris
   Mehdi
   James*/
```

## **7. 화살표 함수**

화살표 함수를 이용해 짧고 쉽게 함수를 작성할 수 있습니다.

일반 함수 *:*

```jsx
function addNums(x, y){
 return x + y;
}

addNums(6, 5); //11
```

화살표 함수*:*

```jsx
const addNums =(x, y)=> x + y;

addNums(6, 5); //11
```

## 결론

위와 같이 JavaScript에서 사용할 수 있는 단축표기법입니다. 이를 통해 코드 구문을 줄이고 코드를 최대한 짧게 유지할 수 있습니다. 그러나 코드를 짧게 하는것이 정답만이 아닙니다. 가독성을 신경써서 쓰는걸 추천드립니다.
