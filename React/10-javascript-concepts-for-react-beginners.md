# [10 JavaScript concepts for React beginners(React 초보자를 위한 10가지 JavaScript 개념)](https://dev.to/sm0ke/10-javascript-concepts-for-react-beginners-4j6n)

React는 JavaScript 프론트엔드 라이브러이며, 시작 하기 위해서는 JavaScript를 배워야 합니다. 그러나 JavaScript는 거대한 개념의 언어이기 떄문에 모든 것을 배우기에는 어렵습니다. 그래서 이 글에서는 React를 배우기 위해 알아야 하는 10가지의 JavaScript 개념에 대해 다룹니다.

## ✨ JavaScript이란?

JavaScript는 웹 페이지용 높은 수준의 스크립트 언어입니다. 1995년 Brendan Eich에 의해 만들어졌습니다. 처음엔 JavaScript는 interactive(상호작용하는) 웹페이지로 만드는 것이 목표였습니다. 그러나 이후에 개발자들이 거의 모든 플랫폼에서 JavaScript를 사용할 수 있도록 프레임워크와 라이브러리를 구축하였습니다.

예를들어 ReactNative를 이용해 모바일 앱을 구현하며, Electron을 이용해 데스크탑 앱을 구축 할 수 있습니다. 백엔드는 Node.js를 이용해서, 프론틍네드는 Ract.js 등을 이용해서도 구축이 가능합니다.

위에서 언급한 내용들은 JavaScript로 할 수 있는 몇 가지 일에 불과합니다. JavaScript로 할 수 있는 모든 것을 말하면 그것도 하나의 글을 작성해야 할 정도입니다.

그러다면 계속해서 React에서 대해서 알아봅시다.

## ✨ React란?

React는 Facebook에서 만들고 유지관리하는 JavaScript 프론트엔드 라이브러리 오픈소스 입니다. React는 컴포넌트 기반 라이브러리로, 큰 애플리케이션을 구성하기 위해 컴포넌트라는 작은 조각을 의미하는 것입니다. React를 통해 큰 프로젝트를 더 쉽게 구축하며, 관리가 가능합니다.

React를 처음 들어본 경우에는 React는 Angular와 같이 프레임워크가 아닌 라이브러리라는 것을 유의해야 합니다. React로 앱을 빌드할 때는 Routing, Http 요청, 상태 관리까지 모두 외부 라이브러리를 사용해야 합니다.

이것은 React에 대한 짧은 설명입니다. 더 자세히 알 고 싶은 경우에는 글 아래에 몇가지 링크를 추가해놓겠습니다.

그렇다면 이제 리액트를 배우기 전에 알아야 할 JavaScript의 개념에 대해서 이야기 해봅시다.

## ✨ 변수(Variables)

React를 알기 위해서는 JavaScript에서 변수를 선언하는 방법을 알아야 합니다. var, let, const를 이용해 변수선언이 가능하며, 각각에 대해서 장단점이 존재합니다.

```jsx
var foo = "hello world!";
let bar = "Hey, There!";
const baz = "What's up?";
```

var는 전역 범위(global scope)에서의 변수를 의미하기 때문에 let,const를 사용하는것이 좋습니다. let는 블록 범위({ })의 변수 선언이며, 해당 범위 내에서 다른 값으로 선언이 가능합니다. const는 상수 값으로 한번 선언 된 이후에 다른 값으로 선언이 불가능 하며, 맨 처음 선언 된 값을 유지합니다.

더 궁금하다면 아래 글을 참고해보세요.

[https://www.freecodecamp.org/news/var-let-and-const-whats-the-difference/](https://www.freecodecamp.org/news/var-let-and-const-whats-the-difference/)

## ✨ 함수 정의(Functions Definition)

React의 기본 규칙은 큰 프로젝트를 작은 컴포넌트로 나누는 것으로 이러한 컴포넌트는 함수 또는 클래스입니다.

따라서 JavaScript로 함수를 선언하는 방법을 아는 것이 굉장히 중요합니다. 또한 변수와 마찬가지로 함수를 선언하는 방법도 아래와 같이 2가지가 있습니다.

**Function Declaration(함수 선언)**

```jsx
// function declaration
function add(num1, num2) {
  return num1 + num2;
}

add(2, 2) // 4

```

일반적으로 자바스크립트에서 함수를 선언하고 호출하는 방식입니다.

자바스크립트로 간단한 함수를 선언하는 방법을 알았으니, 이제 리액트에서 어떻게 사용하는지 살펴보면, React에서 함수는 기능적 구성 요소를 만드는데 사용합니다.

```jsx
function App() {
  return (
    <div className='App'>
      <h1>Hello world!</h1>
    </div>
  );
}
```

위의 예제는 React에서 함수 선언을 사용하는 방법으로 구성 요소는 값을 반환하지 않으며 HTML을 반환하고 있습니다.

**Arrow Function**

```jsx
// Arrow function
const subscract = (num1, num2) => {
  return num1 - num2;
}

subscract(5, 2) // 3

```

함수 선언보다 좀 더 깔끔하게 화살표 함수를 사용할 수 있으며, 이 둘 사이의 큰 차이가 없습니다.

```jsx
const App = () => {
  return (
    <div className='App'>
      <h1>Hello world!</h1>
    </div>
  );
}

```

리액트에서 사용한 것을 봐도 함수 선언이 조금 더 깔끔하고 직관적으로 보이는 것 정도입니다.

## ✨ 클래스와 Extend 키워드(Classes and the Extend keyword)

앞에서 말했듯이, React에는 Class component와 Functional 컴포넌트가 있습니다. Functional 컴포넌트는 함수으로 구현되며, 클래스 구성요소는 Class로 구성됩니다.

자바스크립트 예를 살펴보고 react class 컴포넌트에 대해서 살펴보겠습니다.

```jsx
// javascript class
let Rectangle = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};

const square = new Rectangle(10, 10);

console.log(square.height); // output: "10"

```

JavaScript의 클래스는 객체를 생성하기 위한 템플릿일 뿐입니다. new 키워드를 사용해 클래스에서 새로운 객체를 만들 수 있습니다. extend 키워드를 사용해 새로운 객체도 생성이 가능합니다.

```jsx
class App extends Component {
  render() {
    return (
      <div>App</div>
    )
  }
}

```

여기서 컴포넌트 클래스를 확장해 클래스 컴포넌트를 생성하였으며, HTML/JSX를 반환하는 자바스크립트 클래스 입니다.

## ✨ Async/Await

Async-await는 자바스크립트 개념에서 가장 중요합니다. 그러나 우리는 비동기 자바스크립트를 먼저 이해해야 햡나다.

> Asynchronous JavaScript(비동기 자바스크립트)
> 

웹,앱을 빌드할때 백엔드에서 데이터를 가져오기 위해 API를 호출해야 하며, 이러한 fetch 작업을 완료하는데 시간이 걸릴 수 있습니다.

데이터를 가져오는 동안에는 다른 모든 기능들이 중지됩니다. 자바스크립트는 이러한 문제점을 방지하기 위해 비동기를 도입했습니다. 비동기 합수는 API에서 데이터를 가져오는 동시에 나머지 애플리케이션 코드를 중단하지 않고 동작시키기 위해 도와줍니다.

**이것이 바로 비동기 자바스크립트 입니다.**

비동기 코드를 처리하는 방법이 몇가지 있지만, 이번에는 async/await에 대해서만 이야기 해보겠습니다. 현재로써는 비동기 코드를 처리하기 위한 가장 좋은 방법이기 떄문입니다.

```jsx
async function getPersonsInfo(name) {
  const people = await server.getPeople();
  const person = people.find(person => { return person.name === name });
  return person;
}

```

**Async:**

async는 비동기 함수임을 선언합니다. 비동기 함수는 자동으로 promise를 리턴합니다. async 키워드는 화살표 함수, 콜백 함수, 함수 표현식과 같은 모든 유형의 함수에서 사용이 가능합니다. 

**Await:**

await는 작업이 마칠때 까지 기다리는 비동기 함수를 의미하며, Promise의 .then()과 동일하며, 훨씬 깔끔하게 동작합니다.

그러나 await는 비동기 함수 내에서만 사용이 가능합니다. 그렇지 않으면 오류가 발생합니다.

> Handling Errors(오류처리)
> 

await 키워드는 promise가 해결 될때까지 기다리다가 결과를 리턴합니다. 우리는 promise가 reject될 수 있음을 명심해야 하며, 이걸 처리할 방법이 필요합니다.

비동기 함수의 오류를 처리할 수 있는 방법은 2가지입니다.

**해결법 #1** -  `try ... catch()`:

```jsx
asyncFunctionCall().catch(error => {
  console.error(error)
});
```

**해결법 #2** - Inner `try ... catch()` :

```jsx
async function getPersonsInfo(name) {
  try {
    const people = await server.getPeople();
    const person = people.find(person => { return person.name === name });
    console.log(person)
  } catch (error) {
    console.error(error)
  }
}
```

**Async/await in React**

```jsx
const App = () => {
 useEffect(() => {
  // declare the data fetching function
  const fetchData = async () => {
    //get the data
    const data = await fetch('<https://yourapi.com>');
  }

  // call the function
  fetchData()
  // make sure to catch any error
    .catch(console.error);
    }, [])

  return <>...</>;
};
```

## ✨ Array Method

Array Methods은 자바스크립트에서 배열을 조작하느 방법입니다. method들이 어떻게 동작하는지 알면 유용할 것입니다. react 프로젝트를 시작하면 항상 사용하게 되기 떄문입니다.

> **array.map()**
> 

`array.map()` 은 각 배열 요소에 콜백 함수를 적용해 새 배열을 만들며, 원래 배열을 변경하지 않습니다.

**Code Sample**

```jsx
const numbers = [2, 3, 4, 6];
const newArr = numbers.map(myFunction)

function myFunction(num) {
  return num * 2;
}

console.log(numbers); //2,3,4,6
console.log(newArr); //4,6,8,12
```

> **array.filter()**
> 

`array.filter()`  은 배열의 모든 요소를 순회하면서 콜백 함수의 조건과 일치하는 요소를 필터링 합니다. map()과 마찬가지로 이전 배열의 값을 변경하지 않습니다.

**Code Sample**

```jsx
const ages = [32, 33, 16, 40];
const result = ages.filter(checkAdult);

function checkAdult(age) {
  return age >= 18;
}

console.log(ages);
console.log(result);

```

> Example in React for `map()`
> 

```jsx
function App() {
  const names = ['sam', 'jack', 'tom', 'dave'];

  return (
    <div className='App'>
      <h1>Hello world!</h1>
      {
        names.map(name => <h2>hello {name}</h2>)
      }
    </div>
  );
}

```

위 예제에서는 배열에 map()을 사용해서 names 배열의 요소를 랜더링 합니다. 이것은 매우 유용하게 사용될 것 입니다.

> Example in React for `filter()`
> 

```jsx
const numbers = [1, 2, 3, 4, 5, 6, 7];
const lessThanFive = numbers.filter(num => num < 5);

console.log(lessThanFive); //1,2,3,4
console.log(numbers) //1, 2, 3, 4, 5, 6, 7

```

위 예제는 5 미만의 요소 값만 필터링 해서 보여줍니다.

## ✨ 삼항 연산자

삼항 연산자는 if/esle문의 단순화된 형태입니다. 기본적으로 if-else 문을 작성하는데 또 다른 방법 입니다.

> **Code Sample** - Classic `If/else`
> 

```jsx
//if/else
if (loading) {
  loadingComponent();
} else {
  App();
}
```

> **Code Sample** - Ternary Operator
> 

```jsx
loading ? loadingComponent() : App();
```

> **React Sample** - Ternary Operator
> 

```jsx
class App extends Component {
  render() {
    const isLoggedIn = this.state.isLoggedIn;
    return (
      <div>
        The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
      </div>
    );
  }
}
```

## ✨ Template Literal

웹, 앱을 빌드할때 문자열로 작업하는 건 일반적으로 많습니다. 이전 버전인 ES6에서 문자열을 추가하려면 + 연산자를 사용했어야 합니다. 그러나 이렇게 사용하면 가독성이 떨어지는 단점이 있었습니다. ES6부터는 템플릿 리터럴을 사용할 수 있습니다. `${}` 표기법을 사용해 문자열을 변수와 연결할 수 있습니다.

**Code Sample**

```jsx
const name = "Sam";
const greeting = `Hello, ${name}!`
console.log(greeting) // Hello, Sam!
```

## ✨ Destructuring

Destructuring는 Object 또는 Array 값을 변수로 추출하는 방법입니다.

ES5에서는 아래와 같이 배열이나 객체의 값을 추출하려면 아래와 같이 해야 했습니다.

```jsx
//Array
const foo = ['one', 'two'];
const one = foo[0]

const bar = ["Tom", "Jerry"];
const tom = bar[0]

```

ES6부터는 아래와 같이 구조분해 할당으로 가능해졌습니다.

```jsx
const foo = ['one', 'two'];
const [one] = foo;

//Object
const bar = ["Tom", "Jerry"];
const {tom} = bar

```

하나의 변수의 하나의 값을 할당할 수 있습니다. 이것이 훨씬 가독성이 높아집니다.

> **Example In React.js**
> 

컴포넌트 데이터를 전달하기 위해 props를 사용하는데, Props는 일반 함수의 인자와 같습니다.

여기서 요점은 Props는 객체입니다. 예를 들어 `Greeting` 이라는 컴포넌트가 있고 여기서 props를 사용하면 아래와 같이 사용할 수 있습니다.

```jsx
<Greeting name={"Sam"} />

If you want to access it, you need to write props.name.
function Greeting(props) {
  return (
    <div>hello {props.name}</div>
  )
}

```

그러나 구조분해 할당을 사용하면 아래와 같이 사용하면 됩니다.

```jsx
function Greeting({ name }) {
  return (
    <div>hello {name}</div>
  )
}

```

이제 props.name이 아닌 name으로 사용이 가능합니다.

## ✨ Spread Operator

ㄷ배열 또는 객체의 전체 또는 부분적으로 복사하기 위해 사용됩니다.

상태를 변경할 수 없기 때문에 react state를 사용할 떄 배우 유용 합니다.

```jsx
const arrayOne = [1, 2, 3];
const arrayTwo = [4, 5, 6];
const arrayThree = [...arrayOne, ...arrayTwo];

```

## ✨ Import and Export

Rect는 컴포넌트 기반 UI 프레임워크입니다. 따라서 완전한 애플리케이션을 빌드하기 위해서는 컴포넌트를 사용해야 합니다.

컴포넌트를 사용하기 위해선 파일로 컴포넌트를 가져와야 하는데, export와 import를 사용할 수 있습니다.

> **Export Sample**
> 

```jsx
// app.js file - Exports the “App” object
function App() {
  return (
    <div>App</div>
  )
}
```

> Import Sample
> 

```jsx
// index.js - Use the “App” object via an “import”
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

```

## ✨ Code `Hello World` project with React

React와 JavaScript를 이용해 ‘Hello World’ 프로젝트를 코딩해보려고 합니다.

> **Step#1: Install the tools**
> 

Node.js를 설치합니다.

> **Step#2: Build the app**
> 

프로젝트를 폴더를 아래 명령어를 통해 만듭니다.

```
$ mkdir hello-react

```

`CRA` (create-react-app tool)를 사용해 react 프로젝트를 만듭니다.

```
$ npx create-react-app my-app
$ cd my-app
$ npm start

```

위의 명령어를 실행한 이후 3000 port 번호로 브라우저에서 프로젝트를 엽니다.

```jsx
import React from 'react'

function App() {
  return (
    <div></div>
  )
}

export default App
```

div내에서 h1 태그를 이용해 Hello world!를 작성합니다.

```jsx
import React from 'react'

function App() {
  return (
    <div>
    <h1>hello world!</h1>
    </div>
  )
}

export default App

```

이 후에 브라우저에서 Hello World 메시지가 나오면 됩니다.

## ✨ 결론

JavaScript의 기본 개념을 이해하지 않고 React 학습을 시작하면 비 생상적이고 React의 모든 기능을 사용하기 어려울 수 있습니다.
