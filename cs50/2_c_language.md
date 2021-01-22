# 2. C언어

## 1) **C언어**

```c
#include <stdio.h>
int main(void) {
    printf("hello, world\n");
}
```

- C는 아주 오래되고 전통적인 순수 텍스트 기반의 언어입니다. int main(void)는 쉽게 **시작한다**라는 의미를 가진다고 생각하면 됩니다.
- `printf` 라는 함수로 "hello, world"를 콘솔에 출력할 수 있도록 합니다. 이때, 주의해야 할 점은 글자, 단어, 문장을 적을 때 언제나 값에 쌍따옴표로 감싸줘야 합니다.
- C언어에서는 문장에 마침표를 찍는 것처럼 세미콜론을 붙여야 하며, \n은 ENTER의 의미인 줄바꿈이라고 생각하면 됩니다.
- `#include <stdio.h>` 라는 것은 stdio.h라는 이름의 파일을 찾아 printf 함수에 접근할 수 있도록 해주는 것입니다.
- C언어의 확장자는 .c 입니다.

### **컴파일러**

- 우리가 작성한 코드는 "소스 코드"라고 하며, 컴퓨터가 이해할 수 있도록 머신 코드로 변환하는 작업을 컴파일러라고 합니다.
- clang이라는 명령어로 C언어의 소스코드를 쉽게 컴파일 할 수 있습니다. 그러면 결과로 .out 확장자를 가진 파일이 나오게 되며 그 파일을 실행하면 출력값이 나오게 됩니다.

### **생각해보기**

아래의 실습하기로 "hello, boostcourse"를 출력해보세요.

```c
#include <stido.h>
int main(void) {
	printf("hello, boostcourse\n");
}
```

## 2) 문자열

- CS50 Sandbox에는 get_string 함수로 콘솔에서 입력값을 받을 수 있습니다.
- 여기서 string은 단어, 구절, 문장을 부르는 데이터 타입이라고 생각하면 됩니다.
- 입력받은 걸 저장하는 곳을 변수라고 합니다.

```c
string answer = get_string("너 이름이 뭐야?\n");
```

- 변수명은 자유롭게 정해주면 됩니다. 그러나 변수가 저장하는 데이터 종류를 정확하게 명시해줘야 합니다.
- 아울러 변수에 값을 대입할때는 `=` 를 사용합니다. 즉, 오른쪽에 있는 것을 왼쪽에 저장한다는 의미로 할당 연사자라고 합니다.

```c
#include <stido.h>
#include <cs50.h>

int main(void) {
	string answer = get_string("너 이름이 뭐야?\n");
	printf("hello, %s\n", answer);
}

// 너 이름이 뭐야?
// 럿고
// hello, 럿고
```

- 변수를 출력할때 어떤 데이터 타입의 인자를 받을지 말해줘야 하며, 이때 사용하는 것이 `%s` 입니다.

- 위의 코드를 실행하려면 아래와 같이 명령어를 입력해 컴파일해야 합니다.

```c
$ clang -o string string.c -lcs50
```

- 여기서 -o string 은 string.c 를 string.out 이라는 머신코드로 저장하도록 하는 명령어입니다.
    - lcs50은 “link”라는 의미를 지닌 -l 이라는 인자에 우리가 추가로 포함한 “cs50” 파일을 합친 것입니다. 이를 통해 컴파일시 cs50 파일을 연결하도록 알려줄 수 있습니다.
- 또한 간단하게 아래와 같이도 가능합니다.

```
$ make string
```

### **생각해보기**

"좋아하는 동물을 알려주세요"로 질문하여 동물 이름을 animal이라는 변수에 저장하고, 이를 "내가 좋아하는 동물은"으로 출력해주는 코드를 작성해보세요.

```c
#include <stido.h>
#include <cs50.h>

int main(void) {
	string animal = get_string("좋아하는 동물을 알려주세요\n");
	printf("내가 좋아하는 동물은 %s\n", animal);
}
```

## 3) 조건문과 루프

```c
int count = 0;
```

- 위와 같은 코드는 count라는 변수에 Integer(숫자)인 0을 저장(초기화)하겠다는 의미입니다.

```c
count = count + 1;
count += 1;
```

### **조건문**

```c
if (x < y) {
	printf("x보다 y가 더 크다.\n");
}
```

- if문은 조건문이라고도 부르며, if ( ) 괄호 안에 검사하고 싶은 조건 들어가면 됩니다. 또한 { } 안에는 조건이 true일 때의 수행 작업을 넣으면 됩니다.

```c
if (x < y) {
	printf("x보다 y가 더 크다.\n");
} else {
	printf("y보다 x가 더 크다.\n");
}
```

- else를 이용해 위 조건이 맞지 않은 모든 경우에 어떻게 수행할 것인지를 적을 수 있습니다. 생략도 가능합니다.

```c
if (x < y) {
	printf("x보다 y가 더 크다.\n");
} else if (x > y) {
	printf("y보다 x가 더 크다.\n");
} else if (x == y) {
	printf("x와 y가 같다.\n");	
}
```

- else if를 이용해 여러개의 조건을 추가할 수 있습니다.
- 여기서 추가적으로 `==` 은 같다를 표현하는 것으로 일치 연산자라고 합니다.

- 이러한 조건문으로 얼마나 효율적으로 적은 메모리, CPU를 사용해 프로그램을 만들 수 있습니다.

### **루프**

```c
while (true) {
	printf("hello, world\n");
}
```

- while( ) 괄호 안에 조건을 넣고 조건이 true 일경우 수행할 내용을 { }안에 넣어 주면 됩니다. 그러나 위와 같은 코드는 무한정 실행이 될겁니다. 원하는 조건을 넣어줘야 하는데 아래와 같이 작성할 수 있습니다.

```c
int count = 0;

while (count < 50) {
	printf("hello, world\n");
	count += 1;
}
```

- 또한 위의 코드를 아래와 같이 for문으로 변경할 수 있습니다.

```c
for (int count = 0; count < 50; count += 1) {
	printf("hello, world\n");
}
```

### **생각해보기**

학습한 다양한 방법을 이용하여 "개발공부는 재미있다!"를 10번 출력하는 코드를 작성해보세요.

```c
#include <stdio.h>

int main(void) {
	int count = 0;
	while (count < 10) {
		printf("개발공부는 재미있다!");
		count++;
	}
}
```

```c
#include <stdio.h>

int main(void) {
	for (int count = 0; count < 10; count += 1) {
		printf("개발공부는 재미있다!");
	}
}
```

## 4) 자료형, 형식 지정자, 연산자

### **데이터 타입**

- 아래 목록은 변수의 데이터 타입으로 사용할 수 있는 것들입니다.
    - bool: 불리언 표현, (예) True, False, 1, 0, yes, no
    - char: 문자 하나 (예) 'a', 'Z', '?'
    - string: 문자열
    - int: 특정 크기 또는 특정 비트까지의 정수 (예) 5, 28, -3, 0
    - long: 더 큰 크기의 정수
    - float: 부동소수점을 갖는 실수 (예) 3.14, 0.0, -28.56
    - double: 부동소수점을 포함한 더 큰 실수
- int는 대략 40억까지 셀 수 있기 때문에 40억게 이상의 데이터를 가진 일부 거대 기업과 같은 상황이 아닌 일반 사용자들은 대부분 정수에 int를 사용합니다.

### **CS50 라이브러리 내의 get 함수**

- CS50 라이브러리는 위와 같은 데이터 타입을 입력값으로 받을 수 있는 아래와 같은 함수들을 포함합니다.
- get_char , get_double , get_float , get_int , get_long , get_string

### **형식 지정자**

- **%c** : char
- **%f** : float, double
- **%i** : int
- **%li** : long
- **%s** : string

### **기타 연산자 및 주석**

- +: 더하기
- : 빼기
- : 곱하기
- /: 나누기
- %: 나머지
- &&: 그리고
- ||: 또는
- //: 주석

### **정수와 실수를 받아서 출력해보기**

이번에는 문자열 대신 나이(정수)를 사용해보겠습니다.

```c
# include <cs50.h>
# include <stdio.h>

int main(void) {
    int age = get_int("what's your age?\n");
    int days = age * 365;
    printf("Your are at least %i days old.\n", days);
}
```

이 코드를 좀 더 간단하게 작성해볼까요?

```c
# include <cs50.h>
# include <stdio.h>

int main(void) {
    int age = get_int("what's your age?\n");
    printf("Your are at least %i days old.\n", age * 365);
}
```

- age라는 변수를 없애버리고 age*365 대신에 get_int 함수를 넣어 365를 곱할 수 있습니다.

```c
# include <cs50.h>
# include <stdio.h>

int main(void){
    printf("Your are at least %i days old.\n", get_int("what's your age?\n") * 365);
}
```

- 첫 코드와 마지막 코드를 비교하면 가독성이 굉장히 떨어지는걸 확인할 수 있습니다. 또한 입력받으려고 하는것이 무엇인지를 명확히 알기도 어렵습니다. 읽기 편하고 이해하기 쉬운 코드를 사용하는 것이 더 좋습니다. 그러기 위해 변수나 함수를 사용합니다.

```c
# include <cs50.h>
# include <stdio.h>
int main(void) {
    float price = get_float("What's the price?\n");
    printf("Your total is %f\n", price*1.0625);
}
```

```c
What's the price?
100
Your total is 106.250000
```

- 여기서 소수점이 6번째까지 나온것이 보기 안좋습니다. 일부분(소수점 2번째 자리까지) 나오게 하려면 아래와 같이 변경해주면 됩니다.

```c
printf("Your total is %.2f \n", price*1.0625);
```

### **짝수인지 홀수인지 알려주는 코드짜기**

```c
#include <cs50.h>
#include <stdio.h>

int main(void) {
    int n = get_int("n: ");

    if (n % 2 == 0) {
        printf("even\n");
    }
    else {
        printf("odd\n")
    }
}
```

### **주석**

C에서는 //로 주석을 달 수 있습니다.

```
// 주석입니다.
```

- 주석이란 이 코드를 무슨일 하는지 설명하는 것입니다.

### **참고) #include<cs50.h> 는 무엇인가요?**

- CS50 수업을 위해 만들어진 **라이브러리** 입니다. 라이브러리는 여러 **함수**들을 모아둔 것이라고 볼 수 있습니다. CS50 수업에서는 여러분들께서 좀 더 쉽게 코딩을 짤 수 있게 **CS50 라이브러리** 안에 여러 함수(get_int, get_double, get_float 등등)를 만들어 두었습니다.
- 반면에 C에는 **표준 라이브러리**도 있습니다. 우리가 처음부터 사용한 #inclue<stdio.h>가 표준 라이브러리 중 하나 입니다. 가장 많이 쓰고 가장 보편적으로 사용하는 라이브러리입니다. 그 밖에도 <math.h>, <time.h> 등 자신이 코딩하는데 필요한 함수들을 그때 그때 라이브러리를 불러와서 다른 사람들이 만들어둔 함수를 사용할 수 있습니다. 이 강좌 이후에 직접 코딩을 해보고 좀 더 공부를 하다 보면 배우실 수 있을 것입니다.
- 그렇다면 sandbox.cs50.io가 아닌 Visual Studio 같은 곳에서 CS50 라이브러리를 **바로 사용**이 가능할까요? 답은 사용할 수 없습니다. 표준 라이브러리는 기본적으로 설치가 되어 있기 때문에 사용이 가능하지만 앞서 말씀드렸듯이 CS50 라이브러리는 수업을 위해 만들어진 라이브러리 입니다. 그래서 sandbox.cs50.io 처럼 미리 설치가 된 곳이 아니면 따로 설치를 하여야만 사용이 가능합니다.

### **생각해보기**

짝수인지 홀수인지 알려주는 코드짜기에 자신의 스타일 대로 주석을 달아보고 다른 수강생은 어떻게 주석을 달았는지 비교해보세요.

```c
#include <cs50.h>
#include <stdio.h>

int main(void) {
    int n = get_int("n: "); // 숫자를 입력 받는다.

    if (n % 2 == 0) { // 짝수인지 확인한다.
        printf("even\n"); // 짝수이면 even을 출력한다.
    }
    else { // 짝수가 아니라면 아래의 출력문을 출력한다.
        printf("odd\n")
    }
}
```

## 5) 사용자 정의 함수, 중첩 루프

### **사용자 정의 함수**

- 동일한 작업을 반복하는 것을 함수로 빼서 사용하는 것입니다.

```c
#include <stdio.h>

void cough(void);

int main(void) {
    for (int i = 0; i < 3; i++) {
        cough();
    }
}

void cough(void) {
    printf("cough\n");
}
```

```c
#include <stdio.h>

void cough(int n);

int main(void) {
    cough(3);
}

void cough(int n) {
    for (int i = 0; i < n; i++) {
        printf("cough\n");
    }
}
```

```c
#include <cs50.h>
#include <stdio.h>

int get_positive_int(void);

int main(void) {
    int i = get_positive_int();
    printf("%i\n", i);
}

int get_positive_int(void) {
    int n;
    do {
        n = get_int("Positive Integer: ");
    }
    while (n < 1);
    return n;
}
```

- 위 코드에서 do ~ while은 한번 실행 후 아래 조건을 확인해 다시 실행할지 체크합니다. 즉, 무조건 한번은 실행됩니다.

### **중첩 루프**

```c
#include <cs50.h>
#include <stdio.h>

int main(void) {
    int n;

    do {
        n = get_int("Size: ");
    }
    while (n < 1);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            printf("#");
        }
        printf("\n");
    }
}
```

### **생각해보기**

사용자 정의 함수를 사용하는 것의 장점은 무엇일까요?

- 코드가 간단해집니다.
- 여러 줄로 이루어진 main 함수를 각 역할에 맞게 함수로 쪼게 가독성이 높아집니다.
- 함수명을 통해 해당 기능을 설명할 수 있습니다.

## 6) 하드웨어의 한계

- 컴퓨터는 **RAM**(랜덤 액세스 메모리)이라는 물리적 저장장치를 포함하고 있습니다. 우리가 작성한 프로그램은 구동 중에 RAM에 저장되는데요, RAM은 **유한한 크기**의 비트만 저장할 수 있기 때문에 때때로 부정확한 결과를 내기도 합니다.

### **부동 소수점 부정확성**

```c
#include <cs50.h>
#include <stdio.h>

int main(void) {
    // 사용자에게 x 값 받기
    float x = get_float("x: ");

    // 사용자에게 y 값 받기
    float y = get_float("y: ");

    // 나눗셈 후 출력
    printf("x / y = %.50f\n", x / y);
}
```

- 나눈 결과를 소수점 50자리까지 출력하기로 하고, x에 1을, y에 10을 입력하면 아래와 같은 결과가 나옵니다.

```
x: 1
y: 10
x / y = 0.10000000149011611938476562500000000000000000000000
```

- 정확한 결과는 0.1이 되어야 하지만, float 에서 저장 가능한 비트 수가 유한하기 때문에 다소 부정확한 결과를 내게 되는 것입니다.

### **정수 오버플로우**

```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    for (int i = 1; ; i *= 2) {
        printf("%i\n", i);
        sleep(1);
    }
}
```

```
...
1073741824
overflow.c:6:25: runtime error: signed integer overflow: 1073741824 * 2 cannot be represented in type 'int'
-2147483648
0
0
...

```

- int는 32개의 비트로 약 40억개 정도 저장할 수 있습니다. 그래서 그 이상을 넘어가면 overflow가 발생하게 됩니다.
- 이런 오버플로우 문제는 실생활에서도 종종 발견됩니다.
    - 1999년에 큰 이슈가 되었던 Y2K 문제는 연도를 마지막 두 자리수로 저장했던 관습 때문에 새해가 오면 ‘99’에서 ‘00’으로 정수 오버플로우가 발생하고, 새해가 2000년이 아닌 1900년으로 인식된다는 문제였습니다.
    - 그리고 세계는 수백만 달러를 투자해서 프로그래머들에게 더 많은 메모리를 활용해서 이를 해결하도록 하였습니다.
    - 또한 다른 사례로 비행기 보잉 787에서 구동 후 248일이 지나면 모든 전력을 잃는 문제가 있었습니다.
    - 왜냐하면 강제로 안전 모드로 진입하였기 때문입니다.
    - 이는 소프트웨어의 변수가 248일이 지난 뒤에 오버플로우가되어 발생하였기 때문이었습니다.
    - 248일을 1/100초로 계산하면 대략 2의 32제곱이 나옵니다.
    - 이를 해결하기 위해 주기적으로 재가동을 하여 변수를 다시 0으로 리셋했습니다.
- 따라서 다루고자 하는 데이터 값의 범위를 유의하며 프로그램을 작성하는 것이 중요합니다.

### **생각해보기**

Y2K와 보잉787과 같은 문제를 방지하기 위해서는 프로그램을 어떻게 설계해야 할까요?

- 여러개의 예외 상황들을 생각해 코딩을 진행한다.
- 여유분의 저장공간을 확보한다.
- 꾸준한 테스트를 진행한다.

## 팀 미션

### 문제 1

```c
여러분은 기존 오프라인에서 리테일 사업으로 유명한 신선식품 업체에 입사한 개발자 입니다.

최근 디지털 트랜스포메이션 이슈에 따라 기존 오프라인으로 운영하던 리테일 사업을 온라인으로 전환하게 되었습니다.

여러분에게는 기존 오프라인에서 일어나던 일들을 온라인 쇼핑몰로 전환하는 임무를 진행하게 됩니다.

성공적으로 첫 임무를 완수하여 팀장님의 신임을 얻고 성취감도 느껴보세요!

  

이제 입사한지 얼마 되지 않았기 때문에 간단한 재고 관리 프로그램 개발의 일부를 맡게 되었습니다.

주문이 들어왔을 때 재고량을 적절하게 관리하지 못 하면 재고가 없는 물품의 주문이 추가로 일어날 수 있습니다. 그러면 고객의 불만이 발생할 수 있기 때문에 재고량을 관리가 필요합니다. 또 이렇게 관리한 데이터를 바탕으로 미래의 재고량 수급에 참고 할 수 있을 것입니다.

 

주문이 들어왔을 때 특정 물품의 재고량을 하나 줄이고 매출액을 더하는 일을 하게 됩니다.

 

기존에 수박의 재고량은 5개였습니다.

이번에 들어온 주문은 수박 3건이며, 해당 물품의 가격은 10000원 입니다.

재고량을 차감해주고 매출액을 구하는 프로그램을 만들어 주세요.

 

또 매출액에서 부가세를 구해주세요. 부가세는 물품가격의 10% 입니다.

매출액은 물품의 가격과 부가세를 합산한 가격으로 구합니다.

 

재고량과 매출액을 구하고 다음의 문구를 출력하도록 프로그램을 구현합니다.

(수업 내용을 바탕으로 C언어로 구현하며, 변수명은 자유롭게 지정합니다.)

 

주문건수 : 0 건

기존 재고량 : 0개

남은 재고량 : 0개

매출액(부가세포함) : 000원
```

```c
#include <cs50.h>
#include <stdio.h>

double getTake(int price, int order);

int main(void) {
    int inven = 5;  // 현재 재고

    // 신규 주문수 및 가격 입력 요청
    int order = get_int("신규 주문수 입력(재고 %i개): ", inven);
    int price = get_int("단가 입력: ");

    if (inven > order) { // 재고 충분할 때 매출 내역서 출력
        printf("\n부코식품 매출 내역서\n");
        printf("품목 : 수박\n");
        printf("주문건수 : %i건\n", order);
        printf("기존 재고량 : %i개\n", inven);
        printf("남은 재고량 : %i개\n", inven - order);
        printf("매출액(부가세포함) : %.0f원\n", getTake(price, order));
    }
    else { // 재고 모자를 때 경고 메시지 출력
        printf("재고가 부족합니다. 기존 재고량 : %i개\n", inven);
    }
}

double getTake(int price, int order) {
    return price * order * 1.1;
}
```

### 문제 2

```c
여러분은 인터넷 은행의 인턴으로 취업을 했습니다.

1년 동안 저축을 했을 때 이율을 원금의 1.2%로 주는 예금이 있다고 합니다.

고객이 목표금액을 입력하면 1년 만기시 받는 원금과 이자의 합계를 출력해 주는 코드를 작성해 달라는 팀장님의 지시를 받았습니다.

예금액에 따라 만기시 받게 되는 금액을 구하는 코드를 작성해 주세요.
```

```c
#include <cs50.h>
#include <stdio.h>

const double interestRate = 0.012;
const double taxRate = 0.154;

int main(void) {
    // 고객에게 예치할 금액 물어보기
    int deposit = get_int("예치 금액을 입력해주세요.\n");

    float interest = deposit * interestRate;  // 이자 = 예치금 * 이자율 1.2%
    float tax = interest * taxRate;      // 과세 = 이자 * 15.4%

    // 예상 수령액 안내
    printf("\n1년 후 예상 수령액은 %.0f원입니다.\n", deposit + interest - tax);
    printf("- 원금합계 : %i원\n", deposit);
    printf("- 세전이자 : %.0f원\n", interest);
    printf("- 이자과세 : %.0f원\n", tax);
    printf("*예상 수령액 = 원금합계 + 세전이자 - 이자과세\n*이자과세는 이자의 15.4퍼센트입니다.\n");  // 참고사항
}
```

### 문제 3

```c
모바일 프로그래밍을 배우고 프로그래머로 취업을 하려고 하고있습니다.

신장개업을 하는 식당의 어플리케이션을 개발해 달라는 업무가 들어왔습니다.

 

이 식당은 손님들의 편의를 위해 모바일앱을 통한 쿠폰이나 서비스를 제공하고자 합니다.

프로모션을 위해 오늘의 메뉴를 할인해서 판매하고 있습니다.

 

의뢰자는 음식점의 앱에 들어와서 요일을 입력하면 해당 요일의 메뉴를 출력해 달라고 요청을 했습니다.

요일을 입력했을 때 해당 요일의 메뉴를 출력해 주는 프로그램을 개발 해서 고객의 요구사항을 잘 구현해 주세요!

 

각 요일별 메뉴는 다음과 같습니다.

 

월요일 : 청국장

화요일 : 비빔밥

수요일 : 된장찌개

목요일 : 칼국수

금요일 : 냉면

토요일 : 소불고기

일요일 : 오삼불고기

출력 예시)

요일을 입력하세요: 화요일                                                                                               
화요일: 비빔밥
요일을 입력하세요: 화요일                                                                                              

화요일: 비빔밥
```

```c
#include <cs50.h>
#include <stdio.h>

const string day[7] = {"월", "화", "수", "목", "금", "토", "일"};
const string menu[7] = {"청국장", "비빔밥", "된장찌개", "칼국수", "냉면", "소불고기", "오삼불고기"};

/*
요일 정보를 숫자로 요청하여 배열된 리스트 순서와 매칭시킴
예를 들어, 2번 선택 시 day[2]와 menu[2] 변수를 불러옴
*/
int main(void) {
    int day_num = 0;
		while(true) {
        printf("0.월 1.화 2.수 3.목 4.금 5.토 6.일\n");
        day_num = get_int("원하는 요일의 숫자를 입력해주세요. : ");

        if(day_num < 7 && day_num > -1) {
            printf("\n%s요일 메뉴는 '%s'입니다.\n", day[day_num], menu[day_num]);
            break;
        }
    }
}
```

## 참고자료

[모두를 위한 컴퓨터 과학 (CS50 2019)](https://www.boostcourse.org/cs112/joinLectures/43415)
