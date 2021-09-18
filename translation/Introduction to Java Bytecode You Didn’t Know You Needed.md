# [Introduction to Java Bytecode You Didn’t Know You Needed](https://medium.com/swlh/introduction-to-java-bytecode-you-didnt-know-you-needed-22654cc34ab8)

모든 Java개발자는 언어 생태계에서 JVM 역할에 익숙합니다. 그러나 대부분 Java개발자들은 JVM의 내부동작에 대해서는 이해하지 못합니다. Java 개발자가 되기 위해 해당 지식이 반드시 필요하지 않지만, JVM을 더 잘 이해한다면 더 좋은 코드를 작성하는데 도움이 됩니다. 왜냐하면 작성하는 모든 코드들이 JVM 내부에서 진행되는 프로세스에 어떤 영향을 미치는지 알 수 있기 때문입니다.

JVM의 작동 방식을 이해하기 위해 JAVA의 바이트코드가 무엇인지 이해해야 합니다. 따라서 이 글은 Java 바이트코드의 모든 내용과 프로그램을 마지막으로 실행할 때 JVM에 미치는 영향에 대해 설명하도록 하겠습니다.

## **자바 바이트코드란?**

만약 Java 프로그램의 플랫폼 독립성에 대해서 어떻게 하는지 들어봤다면 Java 바이트코드에 고맙다고 할 것 같습니다.

Java 바이트 코드는 JVM이 프로그램을 실행하는데 사용하는 명령어 세트입니다. 바이트코드로 생성된 프로그램은 실행 중인 플랫폼과 독립적이기 때문에 바이트 코드를 해석할 JVM이 있는 한 모든 플랫폼에서 프로그램을 실행할 수 있습니다.

### **바이트코드는 어떻게 생성되는가?**

바이트코드는 간단하게 말하면 Java Class를 컴파일의 결과물 입니다. `.class` 파일은 클래스를 컴파일 할 때 얻은 실제 코드를 변환한 바이트코드 명령 세트입니다. 명령어를 해석하고 실행하기 위해서는 JVM과 같은 인터프리터가 필요합니다.

### **Java 바이트 코드를 어떻게 볼 수 있는가?**

디컴파일러를 사용하지 않는 한  `.class` 는 열리지 않습니다. 그러나 디컴파일러가 작동하면 실제로 바이트코드가 표시되지 않고 디컴파일러가 바이트코드를 재번역하는 Java 코드가 표시됩니다.

만약 바이트코드를 보고싶다면 다음 명령을 통해 볼 수 있습니다.

```
javap -c -p -v [.class 파일 경로]
```

- `c` : Java 클래스를 디스어셈블하는데 사용하며, 실제 바이트코드를 보기 위해 사용합니다.
- `p` : 클래스의 private 멤버를 같이 보여줍니다.
- `v` : 스택의 크기, 상수 풀과 같은 자세한 정보를 보여줍니다.

## **JVM 작동 방식이란?**

Java 바이트코드에 대해 자세히 알아보기 전에 JVM이 바이트코드를 처리하는 방법에 대해 이해해야 합니다. 메소드(Method)는 JVM의 Java 코드의 가장 중요한 부분 중 하나입니다. Java 프로그램의 런타임은 사실 JVM에서 호출하는 메소드의 모음입니다.  호출하는 각 메서드에 대해 프레임이라는 것을 생성하고 생성된 프레임을 실행하기 위해 현재 스레드의 스택 맨 위에 놓습니다. 프레임은 실행을 유지하는 데 필요한 로컬 환경으로 구성됩니다. 주로 지역 변수 배열, 피연산자 스택을 포함하고 있습니다. 각각 무엇일까요?

### **지역 변수 배열(Local Variable Array)**

로컬 변수 배열은 이름에서도 알 수 있듯이 메서드에서 사용되는 로컬 변수를 저장하는 데 사용됩니다. 또한 메서드의 인자(Arguments)도 저장합니다. 지역 변수 배열의 0 인덱스에는 메서드의 인자(Arguments)를 저장합니다. 메서드가 정적 메서드 대신 인스턴스 메서드인 경우 0 인덱스는 `this` 메서드를 호출하는 데 사용되는 인스턴스를 가리키는 참조를 저장하기 위해 예약됩니다. 정적 메서드와 인스턴스 매서드로 두개의 메서드로 정의하지만 거의 비슷합니다.

```sql
public String sayHello(int num, String name){
    String hello = "Hello, " + name;
    return hello;
}
    
public static String sayHello(int num, String name){
    String hello = "Hello, " + name;
    return hello;
}
```

둘의 지역 변수 배열은 아래 사진과 같습니다. (JVM 메서드 프레임의 로컬 변수 배열)

![image](https://user-images.githubusercontent.com/53366407/133882506-f833b7f3-2bd1-46c3-851d-bc4b6966c187.png)

### **피연산자 스택(Operand Stack)**

피연산자 스택은 메서드 프레임 내부 워크스페이스입니다. 이름에서도 알 수 있듯이 스택이므로 맨 위에서만 값을 넣고 뺄 수 있습니다. 특정 메서드에 속하는 대부분의 바이트코드 명령어는 값을 스택으로 넣거나 스택에서 빼서 처리합니다. 바이트코드 명령어인 `load` 은 변수 배열에 저장된 값을 스택으로 넣는데 사용합니다. `store` 은 스택에서 값을 꺼내 배열에 저장하는데 사용됩니다. 또한 스택에서 값을 빼서 처리하는 다른 명령어도 있습니다. 예로 `add` 는 스택에서 최상위 값 2개를 꺼내서 함께 추가하는 명령

바이트코드 명령어 `load` ****와 그 확장은 변수 배열에 저장된 값을 스택으로 푸시하는 데 사용됩니다. 명령어 `store`는 스택에서 값을 꺼내 변수 배열에 저장하는 데 사용됩니다. 또한 스택에서 값을 처리하여 처리하는 다른 명령이 있습니다.

이러한 시나리오의 예로는 `add` **는** 스택에서 최상위 값 2개를 꺼내서 추가하는 명령과 최상위 값(숫자는 메서드에서 허용하는 매개변수 수에 따라 다름)을 팝하는 메서드 호출 명령이 있습니다. 스택을 사용하여 메서드에 대한 인수로 전달합니다. 이러한 명령에 결과 값이 있으면 스택으로 다시 넣습니다.

```
aload_0 // 변수 배열의 인덱스 0에 있는 기본이 아닌 데이터 값에 대한 참조를 넣습니다.

iload_2 // 변수 배열의 인덱스 4에 int 값을 넣습니다..

iconst_3 // int 3을 스택에 넣습니다.

iadd // 스택의 최상위 int 값 두 개를 추가합니다.

istore_3 // 추가 연산의 결과를 빼고 변수 배열의 인덱스 6에 저장합니다.
```

![image](https://user-images.githubusercontent.com/53366407/133882516-34f0dd05-317b-4d7f-9dfc-9ef3c388cbde.png)

## **바이트코드를 보자**

바이트코드를 볼 수 있도록 간단한 Java 클래스를 작성했습니다.

```java
package demo;

import java.util.ArrayList;
import java.util.List;

public class SimpleClass {

    private List<Integer> evenNums;

    public SimpleClass() {
        evenNums = new ArrayList<>();
    }

    private boolean isEven(int num) {
        return num % 2 == 0;
    }

    public void addEven(int num) {
        if (isEven(num)) {
            evenNums.add(num);
        }
    }
}
```

이제 `javac`명령을 사용하여 클래스를 컴파일하고 `javap` 명령으로 바이트 코드를 확인합니다. 결과는 다음과 같습니다.

```java
Compiled from "SimpleClass.java"
public class demo.SimpleClass {
  private java.util.List<java.lang.Integer> evenNums;

  public demo.SimpleClass();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: new           #2                  // class java/util/ArrayList
       8: dup
       9: invokespecial #3                  // Method java/util/ArrayList."<init>":()V
      12: putfield      #4                  // Field evenNums:Ljava/util/List;
      15: return

 private boolean isEven(int);
    Code:
       0: iload_1
       1: iconst_2
       2: irem
       3: ifne          10
       6: iconst_1
       7: goto          11
      10: iconst_0
      11: ireturn

 public void addEven(int);
    Code:
       0: aload_0
       1: iload_1
       2: invokespecial #5                  // Method isEven:(I)Z
       5: ifeq          22
       8: aload_0
       9: getfield      #4                  // Field evenNums:Ljava/util/List;
      12: iload_1
      13: invokestatic  #6                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
      16: invokeinterface #7,  2            // InterfaceMethod java/util/List.add:(Ljava/lang/Object;)Z
      21: pop
      22: return
}
```

이러한 바이트코드 명령어를 살펴보면 load 및 const 명령을 포함하여 몇 가지 친숙한 명령어를 볼 수 있습니다. 그러나 몇 가지는 어려운 명령어들이 있습니다.

## **바이트코드 분해**

그러나 보이는 것 만큼 어렵지 않습니다. `SimpleClass` 의 바이트 코드를 하나씩 분해해 봅시다. 가장 간단한 메서드인 `isEven` 을 만들어 보겠습니다.

```java
private boolean isEven(int num) {
 return num % 2 == 0;
}
```

```java
private boolean isEven(int);
 Code:
 0: iload_1
 1: iconst_2
 2: irem
 3: ifne 10
 6: iconst_1
 7: goto 11
 10: iconst_0
 11: ireturn
```

1. `iload_1` : 지역 변수 배열의 1 인덱스에 있는 값을 피연산자 스택으로 넣습니다. `isEven` 메서드는 인스턴스 메서드 이기 때문에 0 인덱스에서는 `this` 의 참조값이 저장되어 있습니다. 인덱스 1에 저장된 값이 실제 허용되는 `int num(매개변수)` 값이라는 것을 이해할 수 있습니다.
2. `iconst_2` : 피연산자 스택의 맨 위에 int값인 2를 넣습니다.
3. `irem` : 두 숫자의 나눗셈의 나머지를 찾는 데 사용됩니다. `%`  연산자의 논리를 나타내는 명령어로 스택에서 최상위 값 2개를 빼고 결과를 다시 스택에 넣습니다.
4. `ifne 10` : JVM에 처리된 값이 0이 아닌 경우 지정된 오프셋(10)에 이동하도록 명령합니다. 이 명령은 로직을 구현하기 위해 스택 최상위 요소를 뺍니다. 전달된 숫자가 짝수면 0이 되며, 이 경우 6 인덱스로 이동하도록 명령합니다. 짝수가 아닐때 발생하는 값이 0이 아닌경우 10 인덱스로 이동하도록 명령합니다.
5. `iconst_1` : int값일 1을 넣습니다. 이건 `irem` 의 값이 1일때 발생합니다. 여기서 1은 `true` 값을 나타내는데 사용됩니다.
6. `goto 11` : JVM에 지정된 오프셋(11)로 이동하도록 명령합니다. 이 명령은 테이블에서 한 위치에서 다른 위치로 이동하는데 사용됩니다.
7. `iconst_0` : 스택에 0 값을 스택에 넣습니다. 이 명령어는 `if` 조건이 판명될 대 사용됩니다. 전달된 값인 `0`  은 `false` 로 동작합니다. 즉, 3번, 6번, 7번의 명령어는 `if` 조건이 `true` 경우일 때만 처리합니다.
8. `ireturn` : 스택 맨 위에 있는 int 값을 반환합니다.

위 바이트 코드에서 또 주목해야 하는 것은 바이트 코드에 앞에 숫자는 인덱스 입니다. 모든 명령어 대해 1씩 증가하지 않는다는걸 볼 수 있습니다. 그 숫자는 시작 바이트의 인덱스를 나타내며 모든 바이트코드는 0개 이상의 피연산자가 뒤따라는 1바이트 `opcode` 로 구성됩니다.

`opcode` 는 `iload`, `iconst` 과 같은 명령입니다.  피연산자의 크기에 따라 1바이트 이상으로 변할 수 있습니다. 따라서 명령 테이블의 인덱스에서 볼 수 있는 간격이며 여기에 유일한 2바이트 명령어는 `ifne` 입니다.

`SimpleClass.class` 파일의 바이트 코드에서 `invokespecial`, `invokeinterface` 와 같은 명령어들을 볼 수 있으며 `invokestatic` 는 메서드 호출 명령어 입니다.

각 명령어가 의미하는 바이를 이해하려면 [List of Java bytecode instructions](https://en.wikipedia.org/wiki/List_of_Java_bytecode_instructions)이 글을 보면 좋습니다.
