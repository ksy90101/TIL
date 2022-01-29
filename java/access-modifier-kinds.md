# 자바의 접근제어자(public, protected, private, private-package)

## 접근제어자란?

객체지향에서는 정보 은닉(data hiding)라는 개념이 있습니다. 사용자가 굳이 알 필요가 없는 정보를 사용자로부터 숨겨야 한다는 개념입니다. 최소한의 정보만으로 사용자가 프로그램을 사용할 수 있다는 장점이 있습니다. 즉, 개발자가 개발을 할 때 최소한의 메서드, 클래스만 알고 개발을 진행할수 있다는 점과 동일할 수 있습니다.

총 4가지의 접근 제어자를 자바에서는 지원해주는데 아래와 같습니다.

1. public
2. protected
3. private-package(default)
4. private

접근을 제어하는것이기 때문에 여기서 중요한건 접근을 어디까지 제어할것인가 입니다.

### 접근 범위

| 접근 제어자 | 같은 클래스의 멤버 | 같은 패키지의 멤버 | 자식 클래스의 멤버 | 그 외의 영역 |
| --- | --- | --- | --- | --- |
| public | ○ | ○ | ○ | ○ |
| protected | ○ | ○ | ○ | X |
| private-package(default) | ○ | ○ | X | X |
| private | ○ | X | X | X |

## 접근제어자의 종류

### public

![image](https://user-images.githubusercontent.com/53366407/151647107-77095db3-5d33-4540-8e3b-9ce82081e7d5.png)

쉽게 생각하면 어느 곳에서든지 public 접근제어자를 사용하면 접근을 할 수 잇다고 생각하시면 됩니다.

대부분 private 접근 제어자로 여러개를 선언해놓고, public으로 한번에 부를 수 잇게 하는데요.

그러다 보니 private을 외부에서 부를 수 있는 인터페이스같은 역할도 하고 있습니다.

### protected

![image](https://user-images.githubusercontent.com/53366407/151647105-d9e111cb-531c-4309-8e17-ae981d7cb386.png)

protected는 다른 패키지의 클래스에서 접근을 할 수 없지만, 상속을 한다면 접근은 가능합니다.

### private-package(default)

![image](https://user-images.githubusercontent.com/53366407/151647099-1377cb90-ad6e-473c-9557-f4f14f564023.png)

일단 default라고 부르는 이유는 접근 제어자를 명시하지 않으면, 기본적으로 privatge-package라는 접근 제어자가 적용이 됩니다. 같은 패티지에 있는 것만 접근이 가능하다고 생각하시면 쉽습니다.

### private

어느 외부이던, private 접근제어자가 붙어 있는 멤버나 메서드는 공개되지 않고 접근도 불가능합니다. 즉, 해당 클래스 내에서만 사용하는 메서드를 대부분 적용해놓는 편입니다.

![image](https://user-images.githubusercontent.com/53366407/151647096-a760640e-cc03-4879-b2e4-065ef467c73e.png)

- 여기서 왜 private을 대부분 멤버 변수에 사용할까?
    - 단순하게 클래스를 개발한 개발자가 의도한대로만 멤버를 조회할수 있고 변경하게 할수 있도록 하기 위해서 입니다.
    - 자동으로 캡슐화를 보장해주기 때문에 다른 곳에서 사용하지 않는걸 명시적으로 보여질수가 있습니다.

## 출처

[코딩교육 티씨피스쿨](http://www.tcpschool.com/java/java_modifier_accessModifier)
