# [From Java 8 to Java15 in Ten Minutes(Java8부터 Java15까지 10분안에 보기)](https://medium.com/swlh/from-java-8-to-java-15-in-ten-minutes-f42d422a581e#b9c3)

2020년 가을에 나온 Java15까지 각 Java8부터 Java15까지 최소한 하나의 주요 개선사항을 작성해보도록 하겠습니다.

Java는 람다, 함수형인터페이스, `var` 를 통한 타입 추론, 간단한 생성자와 함께 불변 컬렉션, 멀티라인문자열들을 지원합니다.

또한, 데이터 클래스(record) 그리고 `sealed classes` 와 같은 실험적인 기능이 있습니다.

마지막으로 빠른 실험을 통해 높은 가치를 제공하는 Java REPL에 대해 이야기 하겠습니다.

## 함수형 프로그래밍(Functional Programming) - Java8

Java8에서는 함수형 프로그래밍과 람다가 추가되었습니다.

함수형 프로그래밍의 2가지 중요한 패러다임은 변경불가능한 값(Immutable value)와 함수를 일등 시민으로 올리는 것입니다.

데이터는 수정단계의 파이프라인을 거치며 각 단계에서 입력을 받아 새로운 출력으로 매핑합니다.

함수형 프로그램은 아래와 같이 `Streams` 와 null-safe monads(Optional)과 같이 사용합니다.

### 스트림(**Streams) -Java 8**

평균적인 컴퓨터 프로그램은 값들의 리스트를 받아 각 값들을 변환하는 작업을 수행하는 경우가 많습니다.

Java8 이전에는 `for loop` 를 사용해 변환했지만 이제는 `Streams` 를 사용할 수 있습니다.

```jsx
Stream.of("hello", "great")
    .map(s -> s + " world")
    .forEach(System.out::println);

> hello world
> great world
```

여기서 `map` 함수는 스트림의 모든 요소에 람다를 사용해 적용합니다.

`Streams` 은 `Lists`, `Sets` , `Maps` 에 작업할 수 있으며 덕분에 모든 르프를 제거할 수 있게 되었습니다.

## **Optional - Java 8**

Java의 또다른 흔한 문제는 Null 포인터 예외입니다.

그래서 Java는 Null일 수도 있고 Null이 아닐수도 있는 참조를 래핑하는 모나드인 `Optional` 을 도입하였습니다. 

```java
Optional.of(new Random().nextInt(10))
    .filter(i -> i % 2 == 0)
    .map(i -> "number is even: " + i)
    .ifPresent(System.out::println);

> number is even: 6
```

위의 예제는 난수를 생성해 Optional 객체 안에 래핑한 다음에 짝수인 경우에만 숫자를 출력합니다.

## **JShell - Java 9**

Java도 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop)을 가지게 되었으며 그 이름은 JShell입니다. 간단히 말하자면 Java 클래스를 작성하고 컴파일 하지 않아도 Java 코드를 실행할 수 있습니다.

대신 한 번에 하나의 명령만 실행이 가능하며 즉시 결과를 확인할 수 있습니다.

```bash
$ <JDK>/bin/jshell
jshell> System.out.println("hello world")
hello world
```

JavaScript 또는 Python과 같은 인터프리터 언어에 익숙한 사람들은 REPL의 편리함을 느꼈지만 Java는 이 기능이 없었습니다. JShell을 사용하면 변수를 정의할 수 있지만 여러줄의 함수나 클래스 루프와 같은 복잡한 엔티티를 정의할 수 있습니다.

또한 JShell은 자동완성을 지원합니다.

## 불변 컬렉션을 위한 팩토리 메서드(**Factory Methods for Immutable Collections) - Java 9**

Lists의 간단한 초기화는 오랫동안 Java에 없었지만 이제는 간단하게 가능합니다. 이전에는 아롸애 같았습니다.

```bash
jshell> List<Integer> list = Arrays.asList(1, 2, 3, 4)

list ==> [1, 2, 3, 4]
```

이제는 아래와 같이 간단하게 작성할 수 있습니다.

```bash
jshell> List<Integer> list = List.of(1, 2, 3, 4)

b ==> [1, 2, 3, 4]
```

`of(...)` 는 Map, Set에도 존재하며 한 줄의 코드로 간단하게 변경할 수 없는 객체를 만들수 있습니다.

## `var` 를 이용한 타입 추론 - Java10

Java10부터는 변수 타입을 생략할 수 있는 `var` 키워드가 추가되었습니다.

```bash
jshell> var x = new HashSet<String>()
x ==> []

jshell> x.add("apple")
$1 ==> true
```

위의 예제에서 x 변수는 컴파일러에 의해 HashSet<String>으로 유추할 수 있습니다.

이 기능은 가독성을 향상시켜주며 작성할 코드를 줄여주는 기능을 합니다.

하지만 몇가지 제약사항이 있는데, `var` 는 메서드 내부에서만 사용할 수 있고 컴파일러는 컴파일 시간에 형식을 유추하기때문에 정적으로 형식화가 됩니다.

## 단일 소스 파일 실행(**Single Source File Launch) - Java 11**

이전에는 하나의 파일로 구성된 Java 프로그램을 작성할 때 먼저 `javac` 로 파일을 컴파일 하고 `java` 를 실행해야 되었습니다. 그러나 하나의 명령으로 Java11에서는 한번에 할수 있습니다.

First you define your single source file `Main.java`:

```java
public class Main {
  public static void main(String[] args) {
    System.out.println("hello world");
  }
}
```

```bash
$ java ./Main.java
hello world
```

위 예제처럼 간단한 시작 프로그램이나 단 하나의 Java 클래스로 구성된 경우 이 기능을 사용하면 작업이 쉬워집니다.

## **Switch Expression - Java 12**

Java12에서는 Switch 표현식을 제공해줍니다. 아래 예제에서 이전 switch문과 다른점이 무엇인지 알수 있습니다.

```java
jshell> var i = 3
jshell> String s;
jshell> switch(i) {
   ...>     case 1: s = "one"; break;
   ...>     case 2: s = "two"; break;
   ...>     case 3: s = "three"; break;
   ...>     default: s = "unknown number";
   ...> }
jshell> s
s ==> "three"
```

```java
jshell> var i = 3;
jshell> var x = switch(i) {
   ...>     case 1 -> "one";
   ...>     case 2 -> "two";
   ...>     case 3 -> "three";
   ...>     default -> "unknown number";
   ...> };
x ==> "three"
```

요약하면 예전 switch문은 프로그램 흐름에 관련되어 있었고 새로운 switch문은 값으로 해결합니다.

새로운 switch문은 매핑 함수임을 알고 있어야 합니다. 하나의 입력과 하나의 출력이 있는데 이건 함수형 프로그래밍을 도와주기 위한 패턴 매칭 기능입니다. 이건 스칼라에서도 유사하게 사용할 수 있습니다.

주의해야할 몇가지 사항

- `:` 이거 대신 `>` 이걸 사용합니다.
- break가 필요 없습니다.
- 가능한 모든 경우를 고려할때 default를 생략할 수 있습니다.
- Java12에서 이 기능을 사용하기 위해서는 `-enable-preview --sorce 12` 를 사용해야 합니다.

## **Multi-line Strings - Java 13**

JSON이나 XML과 같은 긴 여러줄의 문자열을 정의해야 했던 적이 있습니까? 지금까지는 한 줄로 묶고 줄 바꿈 문자를 사용했겟지만 이제는 multi-line String을 지원합니다.

```java
public class Main { 
  public static void main(String [] args) {
    var s = """
        {
            "recipe": "watermelon smoothie",
            "duration": "10 mins",
            "items": ["watermelon", "lemon", "parsley"]
        }""";
    System.out.println(s);
  }
```

```java
java --enable-preview --source 13 Main.java

{
    "recipe": "watermelon smoothie",
    "duration": "10 mins",
    "items": ["watermelon", "lemon", "parsley"]
}
```

`""` 도 그래도 유지 되고 `\t` 로 유지됩니다.

## Data Classes: record - Java 14

`record` 키워드로 선언하면 자동으로 Getters, 생성자 및 equals() 메서드를 제공합니다.

```java
jshell> record Employee (String name, int age, String department) {}
|  created record Employee

jshell> var x = new Employee("Anne", 25, "Legal");
x ==> Employee[name=Anne, age=25, department=Legal]

jshell> x.name()
$2 ==> "Anne"
```

Scala 케이스 클래스와 Kotiln의 데이터 클래스를 가지고 있습니다. 지금까지는 Lombok을 사용했지만 records로 변경할 수 있습니다.

## **`instanceof` without Cast - Java 14**

이전에도 `instanceof` 가 존재했습니다.

```java
Object obj = new String("hello");
if (obj instanceof String) {
  System.out.println("String length: " + ((String)obj).length());
}
```

그러나 먼저 String 유형인지 확인한 이후에 길이를 검색하기 위해 다시 캐스팅을 하게 됩니다.

그러나 Java14 컴파일러는 자동으로 유형을 유추해줍니다.

```java
Object obj = new String("hello");
if (obj instanceof String mystr) {
  System.out.println("String length: " + mystr.length());
}
```

# **Sealed classes - Java 15**

With the [sealed keyword](https://docs.oracle.com/en/java/javase/15/language/sealed-classes-and-interfaces.html), you can **restrict** which classes can **extend** a given class or interface. Here’s an example:

클래스나 인터페이스 상속에 대해 제한할 수 있는 `sealed` 이 있습니다.

```java
public sealed interface Fruit permits Apple, Pear {
    String getName();
}

public final class Apple implements Fruit {
    public String getName() { return "Apple"; }
}

public final class Pear implements Fruit {
    public String getName() { return "Pear"; }
}
```

실제로 이제는 열거형과 비슷한 패턴으로 지원하게 되며, 새로운 switch와 잘 어울리게 됩니다.
