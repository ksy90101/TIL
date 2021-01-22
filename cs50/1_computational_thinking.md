# 1. 컴퓨팅 사고

# 1) 2진법

## **컴퓨터 과학**

- 문제 해결에 대한 학문으로 입력(input)을 전달받아 출럭(output)을 만들어 내는 과정이라고 생각하면됩니다. 그 과정을 **컴퓨터 과학**이라고 합니다.
- 이러한 입력과 출력을 **표현**하기 위해선 우선 모두가 동의할 **약속(표준)**이 필요한데, 표현 방법인 2진법을 알아보도록 하겠습니다.

## **2진법**

- 우리가 일상에서 사용하는 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 총 10개의 기호로 표현하는 것이 **10진법**입니다. 하지만 컴퓨터에는 이렇게 많은 숫자가 없습니다. 오직 **0**과 **1**로만 데이터를 표현합니다. 이를 2진법이라고 합니다.
- 10진법은 10의 거듭제곱으로 자릿수를 표현하기로 약속을 해놧지만 2진법은 2의 거듭제곱으로 자릿수를 표현하도록 약속을 했다. 즉, 10진수인 3을 2진수로 나타내면 **2¹x1 + 1x1 = 3 즉, 11이다.**
- 이와 같이 2진법을 전기의 on/off 상태를 0과 1로 표현할 수 있습니다.

## **비트**

- 정보를 저장하고 연산을 수행하기 위해 컴퓨터는 비트(bit)라는 측정 단위를 사용한다.
- 비트는 이진 숫자(binary digit)의 줄인말로 0과 1, 두 가지 값만 가질수 있는 측정 단위

### **비트열**

- 하나의 비트는 **0과 1**, 이 두 가지의 값만 저장할 수 있습니다. 이것은 바로 on/off 스위치라고 할 수 있습니다.
- 하지만 비트 한 개는 많은 양의 데이터를 나타내기에 턱없이 부족합니다. 그렇기 때문에 여러 숫자 조합을 컴퓨터에 나타내기 위해 비트열을 사용하는데 8개의 비트가 모여 만들어 진것을 바이트(byte)라고 한다.
- 하나의 바이트에 여덟 개의 비트가 있고, 비트 하나는 0과 1로 표현될 수 있기 때문에 **2^8** **= 256 개**의 서로 다른 바이트가 존재할 수 있습니다.

### **다양한 데이터 표현하기**

![computational_thinking-1](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-1.png?raw=true)

## 생각해보기

### 5를 2진법으로 바꿔보면 어떻게 될까요?

- 2^0 * 1 + 2^1 * 0 + 2^2 * 1 = 5
- 즉, 101이다.

# 2) 정보의 표현

## **문자의 표현**

- 컴퓨터는 문자를 숫자로 표현할 수 있도록 정해진 약속(표준)이 있습니다.
- 그 중 하나가 미국정보교환표준부호 ASCII(아스키코드/American Standard Code for Information Interchange) 이다.

    ![computational_thinking-2](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-2.png?raw=true)

    - 예를들어 A는 이스키코드 기준으로 10진법의 65이기 때문에 아래와 같이 2진법으로 표현할 수 있다.

    ```sql
    **2^6x1** + **2^5x0** + **2^4x0** + **2^3x0** + **2^2x0** + **2^1x0** + 2^0**x1 (64+1) = 10000001**
    ```

    - 소문자 'a'는 숫자로 97부터 시작한다.
    - ASCII에서 소문자는 같은 대문자 글자보다 항상 2^5 만큼 크다. 즉, 이진수에서 2^5의 자릿수만 변경하면 된다.(소문자는 1, 대문자는 0)
    - 기본 ASCII 코드는 7비트만 사용해 모든 문제를 나타낸다. 즉, 2^7개 128개만 가능하다. 그래서 확장 ASCII 코드로 8번째 비트를 추가해 256개를 나타내도록 하였지만 모든 문자를 표현하기에는 한계가 잇어 유니코드가 생기게 되었다.
- Unicode라는 표준에서는 더 많은 비트를 사용하여 다양한 문자 표현이 가능하다.
    - 100만개 이상의 문자를 나타낼수 있지만 유니코드의 첫 128개 문자는 ASCII의 128개 문자와 동일하므로 서로 호환이 가능하다.
    - 이모티콘도 가능하게 해준다.
    - 😂(기쁨의 눈물) 이런 이모티콘 까지 표현할 수 있게 해주었습니다. 이 이모티콘은 10진법으로 128,514입니다. 2진법으로는 11111011000000010 입니다

## **그림, 영상, 음악의 표현**

- 그림들은 수많은 점들이 RGB(RED, GREEN, BLUE)을 띄고 있는데 각 점들을 픽셀이라고 부른다.
- 무수히 많은 픽셀들의 RGB코드(숫자)로 표현할 수 있게 되며 영상 또한 그림을 연속적으로 이여 붙여놓은 것이기 때문에 숫자로 표현이 가능하다.
- 음악도 각 음표를 숫자로 표현할 수 있다.

---

## **생각해보기**

### 1) CS50을 2진법으로 표현해보세요.

- C : 1000011
- S : 1010011
- 5 : 101
- 3 : 11
- 즉, 1000011 1010011 101 11이다.

# 3) 알고리즘

## **알고리즘**

- 입력 데이터를 받아 출력 형태로 만드는 처리 과정을 알고리즘 이라고 합니다. 즉, 입력값을 출력값으로 변경하기 위해 규칙들의 순서적 나열입니다.
- 많은 알고리즘이 있기 때문에 같은 출력값이라 한다 한들, 다른 시간이 걸릴수 있습니다.

### **정확한 알고리즘**

- 전화번호부에서 특정 데이터를 찾으려고 할때, 한장씩 넘기면서 찾는 거와 반을 줄이고, 다음 또 반을 줄이는 규칙들의 순서적 나열중 어느것이 더 시간이 적게 걸리는가? 후자가 더 적게 걸릴것이다.
- 즉, 알고리즘을 평가할때 정확성도 중요하지만 효율성(작업을 완료하기까지 얼마나 시간과 노력을 덜 들일수 있는가)도 중요하다.

### **의사코드**

- 의사코드는 필요한 행동이나 조건을 잘 설정하여 컴퓨터가 수행해야 하는 일을 절차적으로 파악할 수 있게 도와줍니다.
1. 전화번호부를 **집어 든다**
2. 전화번호부의 중간을 **편다**
3. 페이지를 **본다**
4. **만약** 찾으려는 자료가 페이지에 있으면
    1. 자료를 이용해 **전화를 건다**
5. **그렇지 않고 만약** 앞페이지에 있다면?
    1. 앞 페이지의 절반을 **편다**
    2. **3번부터 다시 실행한다**
6. **그렇지 않고 만약** 뒷 페이지에 있다면?
    1. 뒷 페이지의 절반을 **편다**
    2. **3번부터 다시 실행한다**
7. **그렇지 않다면**
    1. **그만둔다.**
- 노란색 강조된 부분을 함수라고 부른다.
- 함수란 컴퓨터에게 이 경우에는 사람에게 무엇을 할지 알려주는 동사와 같다.
- 파란색으로 강조된 부분은 조건이라고 부른다. 조건뒤에 잇는 문장은 결정을 내리기 위한 질문으로 Boolean이라고 한다.
    - True, False로만 나오는 2진법의 0, 1로 나오는 질문을 뜻한다.
- 분홍색으로 강조된 부분은 루프(loop)라고 하며 반복하는 순환이다.

## **생각해보기**

## 1) 친구와 1부터 100까지 숫자 중 1가지 숫자를 맞추는 스무고개 게임을 하려고 합니다. 이 때 사용할 알고리즘을 의사코드로 표현하면 어떻게 될까요?

1. 1부터 100까지의 숫자 중 하나를 정한다
2. 기회를 20번으로 정한다
3. 친구가 숫자를 하나 고른다
4. 만약 그 숫자가 내가 정한 숫자라면
    1. 게임을 종료한다.(친구 승리)
5. 만약 그 숫자가 내가 정한 숫자가 아니라면
    1. 기회를 1회 감소한다
    2. 3번으로 돌아간다
6. 만약 기회가 0이라면
    1. 게임을 종료한다. (나의 승리)

# 컴퓨팅 사고(Computational Thinking, Scratch)

## **컴퓨팅 사고력이란 무엇일까요?**

- 문제를 해결하기 위한 접근 방법 중의 하나로 문제를 **분해**하고, **어떻게 해결할 것**인지 파악하는 것입니다.
- 컴퓨터는 계산(computation)을 이용해 문제를 해결한다.
- 컴퓨팅 사고는 크게 4가지의 주요한 과정으로 나누어 볼 수 있습니다.

## **분해(Decomposition)**

![computational_thinking-3](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-3.png?raw=true)

- 문제를 나누어 각각의 조각을 확인하거나, 어떻게 문제를 나누면 될 지 생각해내는 과정이죠.

## **패턴 파악(Pattern Recognition)**

![computational_thinking-4](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-4.png?raw=true)

- 이 과정은 ‘어떠한 예측(통찰)’을 위해 각각의 조각의 유사한 점과 차이점을 찾아내는 과정이죠.

### **패턴의 일반화(Pattern Generalization), 즉 추상화(abstraction)**

![computational_thinking-5](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-5.png?raw=true)

- 앞에서 찾아낸 패턴들을 만들어낸 원리를 찾아내는 과정이죠.

### **알고리즘 설계(Algorithm Design)**

![computational_thinking-6](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-6.png?raw=true)

- 이것은 유사한 문제들을 해결하기 위한 절차적인 명령(과정)을 만드는 것입니다.

---

## 컴퓨터과학 vs 컴퓨팅 사고

- 컴퓨터과학은 계산이론과 컴퓨터 응용에 관한 학문 분야
- 컴퓨팅사고는 우리가 문제를 정복해나가는 방법
    - 문제 해결을 위한 큰 그림을 그리기 위한 과정이자, 추상적으로 사고하는 방법

---

![computational_thinking-7](https://github.com/ksy90101/TIL/blob/master/cs50/image/computational_thinking-7.png?raw=true)

- 참고영상: [What is Computational Thinking?](https://youtu.be/sxUJKn6TJOI)

# 팀 미션

## **1. 컴퓨터는 왜 사람이 사용하는 수 체계와 다른 2진수를 사용하게 되었는지, 그리고 왜 기술이 진보한 지금까지도 2진수를 사용하고 있을지 조사와 논의를 통해 정리해봅시다.**

- tip : 정확한 답이 있는 것이 아닙니다. 논의와 검색을 통해 근거있는 이유를 주장해봅시다.

일단 최초의 컴퓨터인 에니악에서는 10진수를 사용했지만 전압을 10단계로 나누어 처리하는 데 한계가 있어 그 다음 버전인 에드박 부터 2진수를 사용했다고 합니다. 이러한 점을 되돌아 봤을때 컴퓨터는 전기적 신호로 통신을 하다보니 언어적인 부분을 이해할수 없어 On/Off, 즉, 0과 1로 이루어져 있는 전기적 신호의 방식인 2진수를 더 편리하다는걸 알 수 있습니다.

또한, 사람은 단어를 보고 단어의 뜻을 바로 떠올릴 수 있지만 컴퓨터는 단순한 방법으로 내용을 처리하는데 그 단순한 방법이 2진수로 표현하는 것이라고 생각합니다.

진보한 지금까지 2진수를 사용할수 밖게 없는 이유는 전체적인 기술은 많이 진보했지만 전기적 신호는 아직 진보하지 않았기 때문에 2진수를 계속 사용한다고 생각하며 혹시 기술이 진보되어 10진수를 사용할 수 있다고 한다 한들 현재 모든 컴퓨터들은 2진수를 사용하고 있는데 10진수로 변경하는 것 자체도 많은 비용이 들기 때문에 2진수를 사용하기도 한것 같습니다.

또한 진수가 올라갈수록 연산속도가 느려진다는 결과가 있는데 컴퓨터는 연산속도가 중요하기 때문에 아직도 2진수를 사용한다고 생각합니다.

```java
전체적인 기술은 많이 진보했지만 전기적 신호는 아직 진보하지 않았기 때문에 2진수를 사용한다고 생각합니다.

최초 컴퓨터인 에니악에서는 10진수를 사용했지만 전압을 10단계로 나누어 처리하는데 한계가 있어서 그 다음 에드박에서 부터 2진수를 사용하게 되었습니다. 

즉, 전기회로는 전압이 불안정해서 전압을 두 단계로 나누어 처리하는 게 안정적이다 보니 2진수를 사용하게 된것이다.

신호의 전압의 차등을 주려면 시간이 소비되는데 2진수를 사용하게 되면 연산속도가 빠를수 있다.

지금 기술이 많이 발전했지만 10진수를 사용하는데도 무리가 없겠지만 2진수를 사용하고 있는 것들을 10진수로 변경하는 것이 많은 비용이 발생하기 때문에 아직까지도 2진수를 사용하는 것 같다.

2진수로 했을 떄 연산속도는 느리고 3진법 이상으로 할 떄는 연산속도가 더 빠르지만 전기적 신호를 구분하는 게 진수가 많아 질수록 비효율적이다.

컴퓨터는 껏다 켰다는 바로 알아볼수 잇지만 반만 켰다 껏다는 애매하기 떄문에 2진법을 더 선호하고 2진법을 사용하고 있기 때문에 2진법의 연산 처리를 속도를 늘리는 것을 개발하려는 노력을 더 하지 않았을까 생각합니다.
```

## **2. 컴퓨터는 아날로그 신호인 소리를 어떻게 디지털로 저장하는 걸까요?**

- 소리가 sin그래프를 그리게 되는데 일정 간격마다의 높이를 저장하는 방식으로 저장한다.
- 시간에 따라 값이 변경되는데 각 시간마다 1초 2초 3초 4초 일때 각 값들이 있을 텐데 값들을 저장한다.

[컴퓨터는 어떻게 소리를 들을까?](https://evan-moon.github.io/2019/07/10/javascript-audio-waveform/)

- 각 문자열, 비디오, 소리 같은 경우 2진수로 변환하는 표준들이 존재하며 표준들을 통해 2진수로 변환하여 디지털로 저장한다.
- 각 음표를 숫자로 표현할 수 있는 표준으로 정해놓고 각 속도나 이러한 것들도 표준을 정해놓고 컴퓨터에게 인식을 시키면 소리를 저장한다.
- 사운드를 저장하는 방식이 PCM(Pulse Coded Modulation)과 ADPCM(Adaptive Differential Pulse Coded Modulation) 있다.
- PCM방식은 입력값 그대로 기록하는 방법으로 사람으로 말하면 절대음감을 가지고 있는 사람이다.
- ADPCM은 차이만 저장하는 방식으로 PCM에서 인접한 값과의 차이가 크푠 진폭을 나누는 단계를 크게 하고, 차이가 작으면 진폭을 나누는 단계를 작게하여 가변적으로 차이를 정밀하게 저장하는 방식입니다. 사람으로 말하면 상대음감을 가진 사람이다.
- 소리도 어차피 진동이기 때문에 음의 높낮이나 진동 세기를 전부 쪼개서 주파수를 측정하여 그걸 이용해서 디지털 신호로 변환하는 작업을 한다.

[아날로그 신호를 디지털 신호로 변환하는 과정](https://blog.naver.com/PostView.nhn?blogId=websearch&logNo=70036950957)

## **3. 내가 사용하는 모니터는 1초에 몇 개의 bit(0과 1)를 사용하여 화면을 표현하고 있을까요? 또, 내가 사용하 는 컴퓨터의 CPU는 1초에 몇 개의 bit(0과 1)를 처리할 수 있을까요?**

1920 * 1020 * 60 * 32

가로 * 세로 * hz * 모니터 표현하는 해상도

- 모니터 픽셀 갯수 / 컬러 비트

- 클럭 = 1초에 CPU가 처리하는 비트 = Hz

[비트와 바이트](https://all-young.tistory.com/7)

- CPU @ 1.60GHz 1.80Ghz
- 1.6 * 10억 = 16억개의 비트를 1초에 처리한다.

## 참고자료

[모두를 위한 컴퓨터 과학 (CS50 2019)](https://www.boostcourse.org/cs112/joinLectures/43415)