# 간단하게 2진수, 8진수, 10진수, 16진수로 변환하기

## Number.toString()

[Number.prototype.toString()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number/toString)

- Number 객체를 문자열로 변환해서 반환하는 메서드입니다.
- 매개변수 중 Optional로 redix를 입력할 수 있습니다.
- 수의 값을 나타내기 위한 사용되기 위한 기준을 정하는 2 ~ 36사이의 정수라고 하는데, 즉 진수를 나타내는 기수의 값이라고 생각하면 될거 같습니다.
- redix 값을 지정하지 않는다면 10진수가 defualt로 설정되게 됩니다.

### 10진수를 2진수로 변환하기

```jsx
const dec = 123;
console.log(dec.toString(2)); // 1111011
```

### 10진수를 8진수로 변환하기

```jsx
const dec = 123;
console.log(dec.toString(8)); // 173
```

### 10진수를 16진수로 변환하기

```jsx
const dec = 123;
console.log(dec.toString(16)); // 7b
```

## parseInt()

[parseInt()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

- 문자열을 분석하여 특정 진수를 사용한 정수로 변환해 반환하는 메서드입니다.
- 매개변수로 필수적으로 string이 들어오게 됩니다. 분석할 값으로 string이 문자열이 아니라면 문자열로 변환하며 문자열의 선행 공백은 무시하게 됩니다.
- 수의 값을 나타내기 위한 사용되기 위한 기준을 정하는 2 ~ 36사이의 정수라고 하는데, 즉 진수를 나타내는 기수의 값이라고 생각하면 될거 같습니다.
- 이때 특징은 defualt값이 10진수가 아니라는 것을 알고 있어야 합니다.
- default 값
    - string이 `0x` 나 `0X` 로 시작한다면 16진수를 반환합니다.
    - string이 `0`으로 시작한다면 8진수 또는 10진수로 구현합니다.  그러나 ECMAScript5에서는 10진수로 구현되지만 많은 브라우저가 이렇게 구현되어있지 않기 때문에 값을 명시하는 것이 좋습니다.
    - 다른 값일 경우에는 10진수 입니다.

### 2진수를 10진수로

```jsx
const bin = "1111011"
console.log(parseInt(hex, 2)); // 123
```

### 8진수를 10진수로

```jsx
const oct = 173;
console.log(parseInt(oct, 8));
```

### 16진수를 10진수로

```jsx
const hex = "7b"
console.log(parseInt(hex, 16));
```

## 심화

- 10진수로 서로 변환하는 것이 아니라 다른 진수들 끼리 변환하고 싶을 수 있다. 그럴땐 어떻게 해야할까?

### 2진수를 8진수

```jsx
const bin = "1111011";
console.log(parseInt(bin, 2).toString(8)); // 173
```

### 2진수를 16진수로

```jsx
const bin = "1111011";
console.log(parseInt(bin, 2).toString(16)); // 7b
```

### 8진수를 2진수로

```jsx
const oct = 173;
console.log(parseInt(oct, 8).toString(2));
```

### 16진수를 2진수로

```jsx
const hex = "7b"
console.log(parseInt(hex, 16).toString(2)); // 1111011
```

### 8진수를 16진수로

```jsx
const oct = 173;
console.log(parseInt(oct, 8).toString(16)); // 7b
```

## 결론

- 코딩테스트에서 진수 변환에 대한 문제는 많이 나오게 됩니다. 이때 이 두가지 Number.toString(), parseInt()만 알고 있다면 충분히 변환하는 것은 문제가 없을 것 같습니다.
