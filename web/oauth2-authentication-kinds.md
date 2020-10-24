# OAuth2 인증방식

## OAuth2 인증 방식 종류

- Authorization Code Grant Type
- Implicit Grant Type
- Resource Owner Password Credentials Grant Type
- Client Credentials Grant Type

## Authorization Code Grant Type

- 권한 부여 코드 승인 타입
- 클라이언트가 다른 사용자 대신 특정 리소스에 접근을 요청할때 사용한다.
- 리소스 접근을 위한 사용자명, 비밀번호, 권한 서버에 요청해서 받은 권한 코드를 함께 활용해서 리소스에 대한 엑스스 토큰을 받아 이를 인증에 사용하게 됩니다.
- 대부분의 소셜 미디어들이 웹 서버 형태의 클라이언트를 지원하는데 사용하는 방식입니다.
- 웹 서버에서 장기 액세스 토큰(long-lived access token)을 사용하여 사용자 인증을 처리합니다.
    - 장기 액세스 토큰

![oauth2-kinds-1](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-1.png?raw=true)

    - 위의 사진은 Facebook에서 제공하는 장기 실행 액세스 토큰 플로우 입니다.
    - 말 그대로 장기적으로 인증을 할수 있는 토큰을 가지고 있으며, 이걸 이용해 토큰이 만료되어 다시 로그인을 해야 합니다.

### Flow

![oauth2-kinds-2](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-2.png?raw=true)

![oauth2-kinds-3](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-3.png?raw=true)

- 리소스 주인은 인증이 필요한 상요자.
- 클라이언트는 웹사이트
- 권한 서버는 소셜미디어
- 리소스 서버는 소셜 미디어

1. 클라이언트가 파라미터로 위의 값들을 보내 권한 부여 코드를 응답 받음
2. 권한 부여 코드와 위의 파라미터들을 이용해 액세스 토큰을 권한 서버에 추가로 요청한다. 
    - 액세스 토큰이란 로그인 세션에 대한 보안 자격 증명 식별 코드로 특정 API 사용을 보증하는 역할
3. 응답받은 액세스 토큰을 사용하여 리소스 서버와 통신

## Implicit Grant Type

- 암시적 승인 타입
- 권한 부여 코드 승인 타입과 다르게 권한 부여 코드 없이 사용자 자격 증명을 교환하는 방식
- 원래는 JavaScript에서 사용하기 위해 만들어 졌지만, 특정 상황에서만 권장합니다.
- 실제로 사용을 권장하지 않는 글도 있습니다.

[Is the OAuth 2.0 Implicit Flow Dead?](https://developer.okta.com/blog/2019/05/01/is-the-oauth-implicit-flow-dead)

### Flow

![oauth2-kinds-4](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-4.png?raw=true)

![oauth2-kinds-5](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-5.png?raw=true)

- 각 역할을 위의 Authorization Code Grant Type와 똑같다.

1. 위의 파라미터를 권한 서버에 전달하여 권한 코드 부여 코드를 응답받는다. 이때 응답 타입은 code, token 인데, token일 경우 암시적 승인 타입에 해당된다. 이때 응답받는 것이 액세스 토큰이다.
2. 액세스 토큰이 유효한지 검증 요청을 하고, 검증 응답값을 받는다.
3. 유효한 엑세스 토큰을 기반으로 리소스 서버와 통신

## Resource Owner Password Credentials Grant Type

- 리소스 소유자 암호 자격 증명 승인 타입
- 클라이언트가 암호를 사용해 엑세스 토큰에 대한 사용자의 자격 증명을 교환하는 방식
- 리소스 소유자가 장치 운영 체제 또는 높은 권한을 가진 응용 프로그램과 같이 클라이언트와 신뢰 관계가 있는 경우에 적합합니다.

### Flow

![oauth2-kinds-6](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-6.png?raw=true)

![oauth2-kinds-7](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-7.png?raw=true)

- Id, Password를 이용해 자격 증명을 클라이언트에게 인증 요청을 한다.
- 인증 요청 정보 기반으로 권한 서버에 엑세스 토큰 정보를 요청해 응답 받습니다. 이때 Refresh Token 정보도 받을 수도 있습니다.
    - Refresh Token : Access Token이 만료되었을 때 새로 발급받을 수 있도록 도와주는 토큰
- Access Token을 이용해 리소스 서버와 통신합니다.

## Client Credentials Grant Type

- 클라이언트 자격 증명 승인 타입
- 클라이언트가 컨텍스트 외부에서 액세스 토큰을 얻어 특정 리로스에 접근을 요청할때 사용합니다.
- 사용자가 앱인 경우에 활용된다.

### Flow

![oauth2-kinds-8](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-8.png?raw=true)

![oauth2-kinds-9](https://github.com/ksy90101/TIL/blob/master/web/image/oauth2-kinds-9.png?raw=true)

1. 엑세스 토큰 정보를 요청해 응답을 받습니다. 이때 별다른 인증 절차가 없기 때문에 Refresh Token까지 넘기지 않는걸 추천합니다.
2. 엑세스 토큰으로 리소스 서버와 통신합니다.

## 참고자료

[처음 배우는 스프링 부트 2](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=168752840&start=slayer)

[OAuth2 인증 방식 정리 - Yun Blog | 기술 블로그](https://cheese10yun.github.io/oauth2/)

[OAuth 2.0 소개 [2부] : OAuth 2.0의 작동 메커니즘](http://www.2e.co.kr/news/articleView.html?idxno=208594)
