## 서론

- ****[[스터디/12기] 단순 CRUD는 그만! 웹 백엔드 시스템 구현(Spring Boot)](https://programmers.co.kr/learn/courses/13469) 이 스터디를 참여해서 쓴 글입니다.**
- 2주차 세션에 대한 내용은 아래와 같습니다.
    
    ### **🗓 2주차**
    
    ### **인증과 인가(Authentication and Authorization)**
    
    ### **2주차 미션 핵심**
    
    - ✅ Spring Security를 활용한 인증(Authentication)
    - ✅ 서비스 확장 시 Session 기반 사용자 인증의 문제점 알기
    - ✅ 장애 대응을 위한 분산서비스 구조
    - ✅ Stateless 아키텍처와 JWT(JSON Web Token)
    
    우리는 보통 웹 백엔드 공부를 시작할 때, 쿠키나 세션을 통한 인증 처리 위주로 배우곤 한다. 그런데 실무에선 서비스가 커지면서 이용자가 많아지면 세션이 무척 바빠지면서 서버가 뻗을 수 있다. 세션 없이, 클라이언트의 상태를 저장하며 서비스 안정성을 취할 방법이 없을까? 여기에 대한 답을 찾아보는 시간.
    
- 스터디 과정은 페이스북과 같은 게시판 서비스를 만들어 보는것이며, 게시판 기능을 만들 때 매주 마다 기능을 추가해 나가면서 개선해 나가는게 목적입니다.
- 2주차 미션은 템플릿코드에 속성을 추가하며, Health-Check를 마늗ㄹ어보며, Voter를 만들어 보는 미션이빈다.

### ID Class

```java
package com.github.prgrms.social.model.commons;

import org.apache.commons.lang3.builder.ToStringBuilder;
import org.apache.commons.lang3.builder.ToStringStyle;

import java.util.Objects;

import static com.google.common.base.Preconditions.checkArgument;

public class Id<R, V> {

  private final Class<R> reference;

  private final V value;

  private Id(Class<R> reference, V value) {
    this.reference = reference;
    this.value = value;
  }

  public static <R, V> Id<R, V> of(Class<R> reference, V value) {
    checkArgument(reference != null, "reference must be provided.");
    checkArgument(value != null, "value must be provided.");

    return new Id<>(reference, value);
  }

  public V value() {
    return value;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Id<?, ?> id = (Id<?, ?>) o;
    return Objects.equals(reference, id.reference) &&
      Objects.equals(value, id.value);
  }

  @Override
  public int hashCode() {
    return Objects.hash(reference, value);
  }

  @Override
  public String toString() {
    return new ToStringBuilder(this, ToStringStyle.SHORT_PREFIX_STYLE)
      .append("reference", reference.getSimpleName())
      .append("value", value)
      .toString();
  }

}
```

- 그렇다면 ID클래스를 사용하는 이유는!?
    - 하나의 서비스에서 Id를 여러개를 받았을때, Long으로 모두 타입을 지정하면, 헷갈림이 있고 휴면에러가 발생할 수 있다.
    - 그럴때 ID클래스를 사용하면 클래스(제네릭)을 이용해 컴파일 단에서 ID에 대한 타입을 더 정확히 체크할 수 있다.
    - 예시는 아래와 같다.
    
    ```java
    @Transactional(readOnly = true)
      public List<Post> findAll(Long userId, Long postId) {
        checkArgument(userId != null, "userId must be provided.");
        checkArgument(postId != null, "userId must be provided.");
    
        postRepository.findById(userId);
        userRepository.findById(postId);
      }
    ```
    
    위와 같은 상황에서, 똑같은 타입이다 보니, 컴파일 단에서 에러를 찾을수가 없다. 그러나 ID 클래스는 제네릭으로 검사하기 떄문에 이러한 에러를 잡을 수 있는 것이다.
    
    ```java
    @Transactional(readOnly = true)
    public List<Post> findAll(Id<Long, User> userId, Id<Post, Long> postId) {
        checkArgument(userId != null, "userId must be provided.");
        checkArgument(postId != null, "userId must be provided.");
    
        postRepository.findById(userId); // 에러
        userRepository.findById(postId); // 에러
      }
    ```
    

## equals, hashcode, toString

- equals는 해당 인스턴스가 같은지를 검사하는 메서드로 equals가 true이면, hashcode는 무조건 같아야 한다.
- 그러나 반대는 성립하지 않는다.
- toString은 디버그나 로깅에서 객체를 사람이 읽기 쉽게 나타내기 위해 사용한다.
- StringBuilder로 나타내는게 가장 좋지만, 기본적으로 String 연산은 StringBuilder로 연산된다.
    - 참고 링크 : [https://siyoon210.tistory.com/160](https://siyoon210.tistory.com/160)

## 인증(Authentication) vs 인가(Authorization)

![image](https://user-images.githubusercontent.com/53366407/158606159-b0565e5d-a5dd-4fce-b670-4cd5f95e5dfe.png)

- 인증(Authentication) : 사용자의 신원을 식별
- 인가(Authorization) : 특정 리소스에 접근할 수 있는 역할을 부여
- 참고 링크 : ****[인증(Authentication) vs 인가(Authorization)](https://rutgo-letsgo.tistory.com/135)****

## Web 서비스 아키텍처

- Rest APIs are everywhere
    - ****[RESTful API 6가지 제약 조건](https://rutgo-letsgo.tistory.com/158)****
- 3-Trier 아키텍처
    
    ![image](https://user-images.githubusercontent.com/53366407/158606125-6af719e4-166b-4869-879e-683f471732a8.png)
    
    - MVC와는 조금 다른 개념이긴 하다.
    - Presentaion - 사용자와 접점이 잇는 곳으로 앱, 웹 브라우저 등 (프론트엔드)
    - Application - 톰캣으로 동작하는 (백엔드)
    - Data - 데이터베이스 (백엔드 & DBA)
    - 가장 큰 장점은 다른 계층에 영향을 최소화 하고 확장에 용의함
    - 스케일 아웃이 가능 → 로드 밸런서를 두고 라운드 로빈 방식으로 부하 분산을 진행하는 것
    - 이론적으로는 Database가 버티기 까지 무한으로 가능하다.
    - 오토 스케일링으로 통해 줄엿다 늘렸다 쉽게 가능하다.
    - 그러나 현실은 Database에서 모든 부하를 버틸순 없다
    - 서버 스펙 확장하면서, 세번째 서버가 장애가 나면 어떻게 되는가?
        - 다른 서버로 로드밸런스가 처리해주지만, 로그인은 다시 해야한다.
        - 그렇다면 이 문제를 어떻게 해결 할 수 있을까?

## Session 기반 사용자 인증

- Session 기반 인증
    
    ![image](https://user-images.githubusercontent.com/53366407/158606083-3792b673-9ca5-4c6e-9c63-44473191c108.png)
    
    - HTTP는 무상태 프로토콜이다.
    - 그럼 어떻게 인증된 사용자를 알 수 있냐? 이걸 바로 Session을 만들고 식별자인 session-id를 클라이언트로 응답한다.
    - 서버 메모리를 사용하기 때문에 너무 많아질 경우 서버 메모리 부족이 발생할 수 있다.
- 해결 방법 (Session Cluster)
    - 조회 속도, 안정성을 위해 In-Memory 분산 데이터베이스를 사용한다.

### Stateless 철학에 충실한 JWT

![image](https://user-images.githubusercontent.com/53366407/158606036-e09117ad-7ce9-4084-aeee-8baea265f3f5.png)

- Session에 저장할 필요 없이 JWT를 발급해주고, 프론트에서 어딘가에 저장해두고 그 다음에 이걸 보내준다.
- 서버는 헤더를 까보고 JWT가 들어있는지 확인하고 JWT를 통해서 클라이언트가 누구였는지 확인하는 것이다.
- Base64 인코딩되어 있는 형태이다. (암호화는 아니다.)
- 암호화된 값이 아니기 때문에, 해당 내용에 민감정보가 들어가면 안된다.
- JWT는 사용자 식별할 수 있는 PK값 같은, 생성 시간, 만료 시간 같은 것을 넣는다.
- Verfy Signature를 넣어서 위변조를 방지하고 있다.
- 참고 링크 : **[JWT를 소개합니다.](https://meetup.toast.com/posts/239)**

## Spring Security 기반 인증&인가 처리

- Spring Security 주요 개념
    - 용어가 어려워서 스프링 시큐리티를 어려워하는 케이스가 많다.
    - Principal(접근주체) : 단순히 사용자. (포괄적이긴 하다.)
        - 인증 전과 인증 후 모두 사용자가 된다.
        - 둘 다 한 단어로 표현된다.
        - Authentication
    - Authenticate(인증) : 사용자 식별
        - AuthenticationMananger, AuthenticationProvider
    - Authorize(인가) : 리소스에 권한이 잇는지 확인
        - AccessDecisionManager,AccessDecisionVoter
    - GrantedAuthority: 인증정보 표현
        - Authentication
    - SecurityContext
        - Principal, GrantedAuthority 개념 둘다를 포괄하는 것
        - Authentication객체를 Wrapping하고있으며, SecurityContextHolder를 통해 접근할수 있음
        - SecurityContextHolder는 기본적으로 ThreadLocal전략을 사용함
- Spring Security Conceptual Architecture
    
    ![image](https://user-images.githubusercontent.com/53366407/158606001-1c9f9986-fa54-43f0-8997-31f43e8f306b.png)
    
    - Authentication Mananger : 인증 처리
    - Access Decisgtion Manager: 인가 처리
- Spring Secuirty Filter
    - 여러개의 필터의 목록이 Spring Security라고 할 정도로 많은 필터가 존개한다.
    
    ![image](https://user-images.githubusercontent.com/53366407/158605961-3bfbfb70-b2d2-4516-929e-f44e864bb3c1.png)
    
    - 주요 필터 종류
        - SecurityContextPersistenceFilter
        - LogoutFilter
        - UsernamePasswordAuthenticationFilter
        - ExceptionTranslationFilter
        - FilterSecurityInterceptor

## 인증 처리

- JWT로 진행
- Authentication use case
    
    ![image](https://user-images.githubusercontent.com/53366407/158605872-53d37d9f-1eeb-4a0e-bd97-43165eab74ec.png)     
    
    - AuthenticationRestController를 확인
        - UsernamePasswrodAuthenticationFilter를 흉내낸것 뿐
    - 그림은 3버전때이고, 현재 코드는 5버전때라 코드가 조금 다를 수 있다.
    - UserPasswordAuthentcationToken이 토큰을 생성해준다.
        - 인증 전 사용자다.
    - 노란색은 인증 전이다.
    - Authentication은 사용자를 의미한다.
    - ProviderManager
        - providers : 이 친구를 봐야한다.
        - authenticate(Auentication authentication) : 여기선 Auentications는 로그인 하지 않는 사용자이며, UserPasswordAuthentcationToken 이다.
        - 여기서 provider.support()에서 true를 발생시켜야 한다.
        - DaoAuthenticationProvider가 true가 발생할것이다.
        - Provider.authenticate가 실제 로그인을 하는 책임을 가지고 있다.
    - 초록색은 인증 후를 이야기 한다.
    - 인증전과 인증 후의 Auentication은 UserPasswordAuthentcationToken로 둘다 똑같다.
- Spring Security 인증 처리 요약
    - 위에서 설명한 내용이 글로 작성해놓은 것이다.
    

## 인가 처리

![image](https://user-images.githubusercontent.com/53366407/158605827-43eb5291-23f7-4e23-833a-c8ddea77d94d.png)

- 여기는 사용자 인증이 끝났고, 인증된 사용자가 적절한 권한을 가지고 있는지 확인하는 것이다.
- AceessDecisionManager는 Interface로 되어 있다.
    - AffirmativeBased : voter가 1개 이상 승인
    - ConsensusBased : 과반수 승인
    - UnanimouseBased : 모든 voter 승인
- AccessDecisionVoter
    - ACCESS_GRANTED : 승인
    - ACCESS_DENIED : 거절
    - ACCESS_ABSTAIN : 보류
    

## JWT로 커스텀 마이징

- 이 방법은 할 수 잇는 방법중 하나이다.
- AuthenticationRestController
    - JwtAuthenticationToken을 생성해, AuthenticationManager를 호출
    - 인증전: 사용자ID
    - 인증후: JwtAuthentication(사용자PK, 이메일)
- JwtAuthenticationProvider(DaoAuthenticationProvider)
    - UserService를 통해 사용자 정보를 데이터 베이스에서 조회
    - 사용자 인증 처리 로직 수행 및 JWT 생성
    - 인증 결과는 JwtAuthenticationToken의 타입으로 반환
- JwtAuthenticationTokenFilter
    - UsernamePasswordAuthenticationFilter 앞에 위치
    - JWT인증결과는 JwtAuthenticationToken타입 으로 SecurityContextHolder에 저장됨
- 인증 전의 Principal과 인증 된 Principal은 다르다.
- 따라서 Principal은 Object 타입을 가지게 된다.
- 인가를 위해서는 Header에 JWT 토큰을 넣어서 프론트에서 보내줘야 인증 인가가 된다.
- @AuthenticationPrincipa을 사용하면 JwtAuthenticationToken.principal(==JwtAuthentication를 참조하게 된다.
