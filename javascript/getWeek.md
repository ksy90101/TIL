# 자바스크립트에서 원하는 날짜의 요일 포맷으로 추출해보자.

- 자바스크립트에는 요일을 따로 추출하는 함수가 존재하지 않는다. 따라서 `Date` 객체를 통해 직접 구현해야 한다.

```java
function getWeek(a, b) {
    const date = new Date(2016, a - 1, b);
    const week = ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'];
    return week[date.getDay()];
}
```

- 위의 코드로  원하는 날짜의 요일을 가져올 수 있다.
- 이때 date.getDay()가 요일을 반환하는데 이때 일요일(0) ~ 토요일(6)의 순서대로 반환하기 때문에 배열을 위와 같이 일요일부터 순서대로 적어줘야 한다.
