## arrayobj.sort(sortFunction)

- sorrtFunction을 생략하면 ASCII 문자 순서로 정렬된다.
- 예를들어 4, 11, 2, 10, 3, 1이 있다면 [1,10,11,2,3,4]로 정렬이 된다.
- 즉, 문자열 정렬 방식을 따르게 된다.
- 그렇다면 숫자를 원하는 오름차순과 내림차순으로 정렬하려면 어떻게 해야 할까?

### sortFunction

- 인수에 함수를 지정하면
- (a, b) ⇒ a > b : -값
- (a,b) ⇒ a = b : 0
- (a,b) ⇒ a < b : +

### 숫자정렬

- 오름차순

```jsx
const numbers = [4, 11, 3, 10, 2, 1]
const sortedNumbers = numbers.sort((a,b) => a - b);
console.log(sortedNumbers); // [1, 2, 3, 4, 10, 11]
```

- 내림차순

```jsx
const numbers = [4, 11, 3, 10, 2, 1]
const sortedNumbers = numbers.sort((a,b) => b - a);
console.log(sortedNumbers); // [11, 10, 4, 3, 2, 1]0: 111: 102: 43: 34: 25: 1length: 6__proto__: Array(0)
```

### 문자 정렬

```jsx
onst fruit = ['orange', 'apple', 'bannana']
const sortFruit = fruit.sort();
console.log(sortFruit) // ["apple", "bannana", "orange"]
```

### Object 정렬

- 이름 오름차순

```jsx
const student = [
    {name: '이지은', age : 28},
    {name: '김유정', age: 25},
    {name: '서강준', age: 30},
    {name: '예리', age: 23}
]

student.sort((a,b) => a.name < b.name ?  -1 : a.name > b.name ? 1 : 0);

// 0: {name: "김유정", age: 25}
// 1: {name: "서강준", age: 30}
// 2: {name: "예리", age: 23}
// 3: {name: "이지은", age: 28}
```

- 이름 내림차순

```jsx
const student = [
    {name: '이지은', age : 28},
    {name: '김유정', age: 25},
    {name: '서강준', age: 30},
    {name: '예리', age: 23}
]

student.sort((a,b) => a.name > b.name ?  -1 : a.name < b.name ? 1 : 0);

// 0: {name: "이지은", age: 28}
// 1: {name: "예리", age: 23}
// 2: {name: "서강준", age: 30}
// 3: {name: "김유정", age: 25
```

- 나이 오름차순

```jsx
const student = [
    {name: '이지은', age : 28},
    {name: '김유정', age: 25},
    {name: '서강준', age: 30},
    {name: '예리', age: 23}
]

student.sort((a,b) => a.age - b.age);

// 0: {name: "예리", age: 23}
// 1: {name: "김유정", age: 25}
// 2: {name: "이지은", age: 28}
// 3: {name: "서강준", age: 30}
```

- 나이 내림차순

```jsx
const student = [
    {name: '이지은', age : 28},
    {name: '김유정', age: 25},
    {name: '서강준', age: 30},
    {name: '예리', age: 23}
]

student.sort((a,b) => b.age - a.age);

// 0: {name: "서강준", age: 30}
// 1: {name: "이지은", age: 28}
// 2: {name: "김유정", age: 25}
// 3: {name: "예리", age: 23}
```

### 정리

- 항상 정렬을 할때는 `sortFunction`를 구현하자.
