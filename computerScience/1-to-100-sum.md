# 1부터 100까지 더하기

## 서론

- 간단하게 1 ~ 100까지 더해 결과를 보여주는 코드를 작성해보려고 한다.
- for문을 맨 처음 배우게 되면 하게 되는 코드이지만, 좀 더 효율적으로 할 수 있는 방법을 같이 설명하고자 한다.

## 기본적인 방법

```java
int sum = 0;
for (int i = 1; i <= 100; i++) {
	sum += i;
}

System.out.println(sum); // 5050
```

- 아주 간단하게 for문을 이용해 처리할 수 있다.
- 이때, 실제 시간복잡도를 고민해보자. for문이 하나 있으며, i의 조건이 늘어날 수록 시간복잡도는 증가하기 때문에 O(n)인걸 확인할 수 있다.

## Stream 이용

- Java8부터는 Stream을 지원하며, 간단한 연산을 지원해주기도 한다.
- 아래와 같이 코드를 작성할 수 있다.

```java
int sum = IntStream.range(1, 101).sum();

System.out.println(sum);
```

- JavaDoc을 참고하면 아래와 같이 Reduce 연산을 사용하는 것으로 보이며, Reduce연산은 실제 저 위의 방식과 동일하기 때문에 O(n)일것 이다.

```
Returns the sum of elements in this stream. This is a special case of a reduction and is equivalent to:

A reduction operation (also called a fold) takes a sequence of input elements and combines them into a single summary result by repeated application of a combining operation, such as finding the sum or maximum of a set of numbers, or accumulating elements into a list. The streams classes have multiple forms of general reduction operations, called reduce() and collect(), as well as multiple specialized reduction forms such as sum(), max(), or count().
Of course, such operations can be readily implemented as simple sequential loops, as in:
 
    int sum = 0;
    for (int x : numbers) {
       sum += x;
    }
 
However, there are good reasons to prefer a reduce operation over a mutative accumulation such as the above. Not only is a reduction "more abstract" -- it operates on the stream as a whole rather than individual elements -- but a properly constructed reduce operation is inherently parallelizable, so long as the function(s) used to process the elements are associative and stateless. For example, given a stream of numbers for which we want to find the sum, we can write:
 
    int sum = numbers.stream().reduce(0, (x,y) -> x+y);
```

## 효율적인 방법 - 가우스

- 조금만 생각해보자. 예를들어 1 ~ 10까지 있다고 해보자.
- 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
- 1 + 10 = 11
- 2 + 9 = 11
- ....
- 5 + 6 = 11
- 즉, 앞에서 순차적으로, 뒤에서 순차적으로 두 값을 더하게 되면 같은 값이 나오게 되는걸 확인할 수 있다.
- 이에 맞춰 코드를 작성해보면 아래와 같이 나오게 된다.

```java
int sum = 0;
int number = 100;
for (int i = 1; i <= number / 2; i++) {
    sum += i + (number - (i - 1));
}

System.out.println(sum);
```

- 맨 처음 기본적인 방법보다 /2가 줄어들었기 때문에 위의 시간복잡도 O(logn)이 될것이다.

- 그러나 위에 처럼 for문을 돌리지 않고 간단한 공식을 유추할수 있을 것같다.
- 즉, number = (100 + 1)* (100 / 2) 이와 같이 어차피 101이 총 50번 나오는 것이기 때문에 위와 같이 작성할 ㅅ후 있을거 같다.

```java
int number = 100;
System.out.println((number + 1) * (number / 2));
```

- 그러나 여기서 문제는 홀수일때다. 예를 들어 1 ~ 101의 총합을 더하게 될때는 51이라는 숫자가 더해지지 않을것 이다.
- 그렇다면 51은 딱 중간에 있는 값이 된다.
- 따라서 아래와 같은 공식이 나올수가 있다.

```java
System.out.println(((number + 1) * (number / 2)) + number / 2 + 1);
```

- 또 다른 방식으로는 맨 뒤에 있는 값을 제외하고 더한다음 맨 뒤에 있는 값을 더하게 되면 실제 위에 처럼 나올수 있을것이다.

```java
System.out.println((number * ((number - 1) / 2)) + number);
```

## 결론

- 가우스가 발견한 공식을 이용해 실제 시간복잡도를 최소한으로 줄일 수 있다.
