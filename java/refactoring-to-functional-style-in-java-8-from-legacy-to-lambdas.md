# [Java8에서 함수형 스타일로 리팩토링 : 레거시에서 람다로](https://medium.com/pragmatic-programmers/refactoring-to-functional-style-in-java-8-from-legacy-to-lambdas-3e36f2911190)

## By Venkat Subramaniam

> Java8에는 함수형 프로그래밍 기능이 추가되었지만, 실제로 함수형 프로그래밍으로 작성하기 위해서는 가이드가 필요할 수 있습니다.
> 

![https://miro.medium.com/max/1400/1*KvVW4vtyKwkcPUgF1SVFIA.png](https://miro.medium.com/max/1400/1*KvVW4vtyKwkcPUgF1SVFIA.png)

[https://pragprog.com/newsletter/](https://pragprog.com/newsletter/)

자바에서 우리는 명령형 스타일을 사용해 객체지향 코드를 작성하였습니다. Java8에선 함수형 스타일로 코드를 작성할 수 있게 되었습니다. 기존 모든 코드를 모두 변환할순 없겠지만 버그를 수정하면서 기존코드를 리팩토링 할때마다 함수형 스타일을 사용할 수 있습니다. 이 글에서 우리는 레거시 코드를 즉시 람다를 사용할수 있는 방법과 왜 변경해야 하는지를 알아보도록 하겠습니다.

# 왜 함수형 스타일인가?

명령형 프로그래밍에서 우리는 코드에게 무엇을 어떻게 해야 하는지를 알려줍니다. 선언적 프로그래밍을 통해 우리는 무엇을 해야 하는지에 초점을 맞추고 기본 라이브러리가 어떻게 처리를 해야 하는지 작성합니다. 함수형 스타일은 선언적이고 고차 함수, 즉 객체를 전달하는 것처럼 함수를 전달할 수 있는 기능을 사용할 수 있습니다.

명령형 프로그래밍은 세부 사항과 변경 가능성을 포함하고 있습니다. 그러면서 코드를 작성하기에 더 복잡하게 됩니다. 함수형 프로그래밍이 명령형 프로그래밍만큼 익숙하지 않더라도 Java 개발자들에겐 많은 사람들에게 함수형 프로그래밍은 상대적으로 덜 복잡하게 됩니다.

구문과 개념에 익숙해지면 명령형 프로그래밍보다 코드가 더 간결하고 가독성이 더 높다는걸 알 수 있습니다.

![https://miro.medium.com/max/1400/1*aoCa3AQEAizPHWQc7HssfQ.png](https://miro.medium.com/max/1400/1*aoCa3AQEAizPHWQc7HssfQ.png)

# 람다와 자바

람다 표현식은 수십년 동안 다른 언어에서 사용되었습니다. 그러나 Java8에 도입되어 Java에선 새로운 기능입니다. 함수와 비교해서 이해할 수 있습니다.

함수엔 네 부분이 있습니다.

1. 반환 타입
2. 이름
3. 매개변수 목록
4. 로직

람다 표현식은 매개변수 목록과 로직으로만 구성되며 `->`를 통해 구분됩니다. 람다는 함수에서 본질을 추출할 수 있습니다. 다음은 Integer 값을 사용해 double을 반환하는 람다 식의 예 입니다.

```java
(Integer number) -> number * 2

```

Java는 내용을 기반으로 매개변수의 유형을 추론할 수 있으며, 단일 ㅐ매개변수에 대한 괄호를 삭제할 수 있습니다. 따라서 아래와 같이 작성할 수 있습니다.

```java
number -> number * 2

```

Java에 람다를 도입하는 것 외에도 Java8로 재 컴파일 되지 않는 레거시 코드와 함께 람다를 사용할 수 있도록 하였습니다.

# 실행 가능한 람다

20년 이상 전에 생성된 클래스를 사용하는 간단한 코드를 살펴보겠습니다.

```java
public static void main(String[] args) {
  Thread thread =
   new Thread(new Runnable() {
    public void run() {
     System.out.println("Hello from another thread");
    }
   });
  thread.start();
  System.out.println("Hello from main");
 }

```

`main()`  메서드에서 `Runnable`  인터페이스의 구현체를 `Thread` 익명 내부 클래스에에 전달해 인스턴스를 만들었습니다. 실행하면 프로그래밍에서 아래와 같이 결과가 나옵니다.

```
Hello from main
Hello from another thread

```

코드를 보면 `Thread` 의 생성자에 객체를 전달하였습니다. 그러나 우리의 주요 의도는 다른 스레드에서 호출하기 위해 실행 가능한 코드의 일부 함수를 전달하는 것으로 생성자에 함수를 전달하고 싶었습니다.

Java에선 함수를 미취학 아동처럼 취급하였습니다 "동네 공원에 가도 될까요?" "안돼, 너 혼자 갈수 없어 어른을 보내줄게." 라는 것 처럼 함수를 단일 메서드로 클래스를 매핑해서만 전달이 가능했지만 Java8부턴 이럴 필요가 없습니다.

`Thread`  클래스의 생성자는 변경되지 않습니다. 그러나 람다식을 사용해 위 코드를 작성할 수 있습니다.

```java
public static void main(String[] args) {
	Thread thread2 = new Thread(() -> System.out.println("Hello from another thread"));
	thread2.start();
	System.out.println("Hello from main");
}

```

객체를 생성하는 대신에 `Runnable`  인터페이스의 run 메서드를 람다 식으로 전달하였습니다. 람다 식은 익명이지만 Thread 클래스의 `run`  메서드의 기능을 충실히 잘 합니다. 람다 표현식을 사용하면 Java8은 메서드 이름대신 Signature(매개변수 유형 및 반환 값)을 중요하게 생각합니다. 호환 가능한 Signature가 있는 람다 식은 단일 추상 메서드 인터페이스의 기능을 대신 할 수 있습니다.

# 람다는 함수형 인터페이스를 대신할 수 있습니다.

다른 언어와 달리 Java는 람다 식을 표현하는 함수 객체를 제공하지 않습니다. 처음엔 인터페이스에 람다 식을 할당할 수 있다는 사실이 어색해 보일수 있지만 이유가 있습니다. 바로 이전 버전과의 호환성 때문입니다.

Java8에서는 단일 추상 메소드 인터페이스가 예상되는 모든 위치에 익명함수인 람다 표현식을 전달할 수 있습니다. 이러한 인터페이스를 `Functional Interface` 라고 합니다. `Runnable`, `Callable`, `FileFilter`, 등과 같은 인터페이스에 모두 람다 식을 전달할 수 있습니다. 위와 같은 이유때문에 클래스 생성자에 람다 식을 전달할 수 있었습니다.

`Callable`을 가지고 다른 예제로 살펴보도록 하겠습니다.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.*;

import static java.util.stream.Collectors.toList;

class YahooFinance {

    private static final String YAHOO_FINANCE_URL = "<http://ichart.finance.yahoo.com/table.csv?s=>";
    private static final String REGEX = ",";

    public static double getPrice(final String ticker) {
        try {
            final URL url = new URL(YAHOO_FINANCE_URL + ticker);
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(url.openStream()))) {
                final String[] dataItems = reader.lines()
                    .collect(toList())
                    .get(1)
                    .split(REGEX);
                return Double.parseDouble(dataItems[dataItems.length - 1]);
            }
        } catch (Exception ex) {
            throw new IllegalArgumentException(ex);
        }
    }
}

public class Ex1 {
    public static String getPriceFor(String ticker) {
        return ticker + " : " + YahooFinance.getPrice(ticker);
    }

    public static void main(String[] args) {
        try {
            List<String> tickers = Arrays.asList("GOOG", "AMZN", "AAPL", "MSFT", "INTC", "ORCL");
            ExecutorService executorService = Executors.newFixedThreadPool(100);
            List<Future<String>> pricesFutures = new ArrayList<>();
            for (String ticker : tickers) {
                pricesFutures.add(executorService.submit(new Callable<String>() {
                    public String call() {
                        return getPriceFor(ticker);
                    }
                }));
            }
            for (Future<String> priceFuture : pricesFutures) {
                System.out.println(priceFuture.get());
            }
            executorService.shutdown();
            executorService.awaitTermination(100, TimeUnit.SECONDS);
        } catch (Exception ex) {
            System.out.println(ex.getMessage());
        }
    }
}

```

`getPriceFor()` 메서드는 시세 표시를 나타내는 매개변수를 받아 시세 표시 이름과 해당 가격을 반환합니다. 내부적으로는 `YahooFinace`의 `getPrice()` 를 호출합니다. 스레드 풀의  `ExecutorService`  을 해서 각각의 주식각격을 가져옵니다. 그리고 `Feture`  가져온 데이터가 `ExcutorService`의 `submit()`메서드에 의해 반환됩니다. 최종적으로 풀을 종료하고 기다리게 됩니다.

`getPrice()` 메서드는 `Yahoo!` URL에 요청을 보내고 주식 데이터를 제공하고 결과를 파싱하고 추출해서 원하는 가격값을 반환받습니다.
실행되면 아래와 같은 결과가 나옵니다.

GOOG : 790.799988
AMZN : 757.77002
AAPL : 115.970001
MSFT : 62.299999
INTC : 36.310001
ORCL : 39.099998

표시된 실제 가격은 시장에 따라 다릅니다.
이제 함수형 인터페이스를 이용해 람다로 작성해봅시다.

In the `main()` method, we can rewrite the part:

```java
pricesFutures.add(executorService.submit(new Callable<String>() {
    public String call() {
        return getPriceFor(ticker);
    }
}));

as

pricesFutures.add(executorService.submit(() -> getPriceFor(ticker)));

```

`new`, `public` , `return`  등등의 키워드가 필요 없어집니다. 함수형 인터페이스가 예상되는 곳에 람다 식을 보내는 것이 첫번째로 가장 좋은 단계이지만 이것보다 더 깔끔하게 작성이 가능하빈다.

# 외부 반복자에서 내부 반복자로 변환

반복을 제어하는 `for` 을 사용하는 대신에 내부 반복으로 제어가 가능합니다.
Java8의 Stream을 사용하면 됩니다.

```java
for (String ticker : tickers) {
    pricesFutures.add(executorService.submit(() -> getPriceFor(ticker)));
}

as

List<Future<String>> pricesFutures = new ArrayList<>();
tickers.stream()
    .map(ticker -> executorService.submit(() -> getPriceFor(ticker)))
    .collect(toList());

```

이건 명령형 스타일보다 더 간결해 보이지 않을수도 있습니다. 또한 `for` 를 변환한다는 것은 `get` 메서드가 어려울수 있습니다. `Future`의 `get` 메서드는 `InterruptedException`, `ExecutionException` 예외를 Throw할수 있습니다.

```java
pricesFutures.forEach(priceFuture ->
      System.out.println(priceFuture.get()));

error: unreported exception InterruptedException;
       must be caught or declared to be thrown

```

# 병렬 Stream 사용

첫번째 예제에서 봤듯이 익명 내부 클래스를 람다식으로 변환하면 복잡함과 코드를 간결하게 만들 수 있습니다. `ExecutorService` 예제에서는 이점이 조금 미미해 보이긴 합니다.

때때로 우리는 함수형 인터페이스를 구현하는 인스턴스를 람다 식으로 변환할때 간단한 방법을 찾아야 합니다. 다른 경우에는 다른 접근 방식이 필요한데, `ExcutorService`가 대표적으로 그렇습니다.

우리의 문제를 간단히 살펴보면 시세 표시 기호의 목록을 가져와 가격을 동시에 찾는것입니다. 이를 위해 병렬 스트림을 사용할 수 있습니다. 자세한 내용은 *[Functional Programming in Java](https://pragprog.com/book/vsjava8/functional-programming-in-java)* 를 참고하세요.

```java
public static void main(String[] args) {
 List<String> tickers =
  Arrays.asList("_GOOG_", "_AMZN_", "_AAPL_",
                "_MSFT_", "_INTC_", "_ORCL_");
 tickers.parallelStream()
        .map(StockPrices::getPriceFor)
        .forEachOrdered(System.out::println);
}

```

코드가 누락된게 아니라 총 18줄이 3줄로 줄어든것 뿐입니다.

우리는 시세 목록을 병렬로 반복해 주식 가격을 가져와 목록의 기호를 나타내는 순서대로 출력합니다. 순서가 중요하지 않은 경우엔 `forEach()` 를 사용하면 됩니다.

기본적으로 병렬 스트림은 런타임 시스템의 코어 수만큼의 스레드를 사용합니다. 그러나 `ExcutorService` 는 스레드 수를 명시적으로 제어했습니다. 병렬 스트림에서 동일하게 사용 가능합니다.

런타임 옵션을 사용해 병렬 스트림의 대한 풀의 스레드 수를 JVM 전체에서 제어가 가능합니다.
`-Djava.util.concurrent.ForkJoinPool.common.parallelism=100`

또한 코드 단에서도 제어가 가능합니다.

```java
public static void main(String[] args) {
   try {
    List<String> tickers =
     Arrays.asList("_GOOG_", "_AMZN_", "_AAPL_",
                   "_MSFT_", "_INTC_", "_ORCL_");
    ForkJoinPool pool = new ForkJoinPool(100);
    pool.submit(() ->
     tickers.parallelStream()
            .map(StockPrices::getPriceFor)
            .forEachOrdered(System.out::println));
    pool.shutdown();
    pool.awaitTermination(100, TimeUnit.SECONDS);
  } catch (Exception ex) {
    System.out.println(ex.getMessage());
  }
}

```

100개의 스레드를 가진 `ForkJoinPool`  를 가진 컨텍스트에서 병렬 스트림을 사용하면 해당 풀의 스레드에서 사용합니다.

이 글에서 우리는 함수형 인터페이스를 람다식으로 변환하는 방법을 봤고 이것을 버그를 수정하거나 리팩토링을 할때 첫번째 단계가 될 수 있습니다.

[https://pragprog.com/search/?q=functional-programming-in-java](https://pragprog.com/search/?q=functional-programming-in-java)
