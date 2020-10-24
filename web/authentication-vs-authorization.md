# 인증(Authentication) vs 인가(Authorization)

![authentication-vs-authorization](https://github.com/ksy90101/TIL/blob/master/web/img/authentication-vs-authorization.png?raw=true)

## 인증(Authentication)이란?

- 사용자의 신원을 증명하는 것
- 예를들어 로그인하는 과정을 인증이라고 한다.
- 로그인을 하게 되면 JWT나 Session을 이용해 인증을 유지한다.

### 인증 방식

- 전통적인 방법으로는 사용자명(Principle), 비밀번호(Credential)로 인증하는 Credential 기반 인증 방식
- OTP와 같이 추가적인 인증방식을 도입해 한번에 2가지 방법으로 인증하는 이중 인증 방식
- 토큰 인증
- 소셜 미디어를 사용하는 OAuth2 인증방식(토큰인증 방식 사용)
  - [OAuth 인증 종류](https://github.com/ksy90101/TIL/blob/master/security/oauth2-authentication-kinds.md)

## 인가(Authorization)란?

- 특정 리소스에 접근할 수 있는 권한을 부여하는 것
- 예를들어 사용자 등급(일반, VIP, 관리자)에 따라 리소스에 접근을 제한하는 경우
- 인가를 하기 위해 인증이 먼저 선행되어야 한다.
- JWT, Session을 이용해 인증을 확인하고, 서버가 해당 권한을 확인해 리소스 접근을 결정한다.
