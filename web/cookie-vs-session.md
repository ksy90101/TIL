# Cookie vs Session

## HTTP의 특징

### Connectionless(비연결지향)

- Client에서 Server에 Request를 보내면 Server는 Client에 Response를 하고, 접속을 끊음

### Stateless(상태정보비유지)

- 비연결지향(Connectionless)로 인해 Client의 상태정보에 대해 알 수 없음
  
## 쿠키(Cookie)

- Client Local에 저장되는 Key-Value쌍의 작은 데이터 파일

### 구성 요소

- 이름, 값, 만료시간, 전송할 도메인명, 전송할 경로, 보안연결여부, HttpOnly여부

  - HttpOnly : 서버로 http Request 요성시에만 cookie를 전송, 스크립트 언어로 브라우저의 cookie를 호출했을때 브라우저가 응답하지 않음

### 동작방식

![cookie-vs-session-1](https://github.com/ksy90101/TIL/blob/master/web/img/cookie-vs-session-1.png?raw=true)

1. Clinet가 Server에 Login Request를 함

2. Server는 Clinet의 Login Request의 유효성을 확인(아아디, 패스워드 검사)

3. 응답헤더에 set-cookie를 추가하여 응답

4. Clinet는 이후 Server에 Request할때 전달받은 쿠키를 자동으로 요청헤더에 추가하여 요청, 헤더에 쿠키값을 자동으로 추가하여 주는데 이는 브라우저에서 처리

  - 쿠키의 기한이 정해져 있기 않고 명시적으로 지우지 않는다면 반 영구적으로 남아있음

3. 쿠키값 확인 방법

  - 브라우저 개발자 도구의 네트워크(가독성이 떨어지고, 수정 불가)

  - 브라우저의 쿠키관리 탭, 쿠키관리 플러그인(쉽게 수정 가능)

  - 쿠키는 Clinet에서 수정할 수 있기 때문에 위변조 위험이 항상 존재

  - 쿠키값 암호화해야 안전

## 세션(Session)

- 브라우저가 종료되기 전까지 클라이언트의 요청을 유지하게 해주는 기술

### 동작 방식

![cookie-vs-session-2](https://github.com/ksy90101/TIL/blob/master/web/img/cookie-vs-session-2.png?raw=true)

1. Clinet가 Server에 Login Request 함.

2. Server는 Clinet의 Login Request의 유효성 확인(아이디, 패스워드 검사)

3. unique한 아이디를 sessionid라는 이름으로 저장

4. 서버가 응답할떄 응답헤더에 set-cookie : sessionid : id 를 추가하여 응답

5. 클라이언트는 이후 서버에 요청할때 전달받은 sessionid쿠키를 자동으로 요청헤더에 추가하여 요청

6. Server에서는 요청헤더의 sessionid 값을 저장된 세션저장소에서 찾아보고 유효한지 확인후 요청을 처리하고 응답

  - 세션 내용은 서버에 저장되기 때문에 늘어날 경우 서버 과부하 발생

## Cookie와 Session의 차이점

### 저장위치
- Cookie는 로컬에, Session은 로컬, 서버에 저장

### 보안
- 쿠키는 탈취, 변조 가능 그러나 세션은 id값만 가지고 있으며, 서버에도 저장되어 있어 상대적으로 안전

### Lifecycle

- 쿠키는 브라우저를 종료해도 파일이 남지만, 세션은 브라우저 종료시 삭제

### 속도

- 쿠키는 파일에서 읽어 오기 때문에 상대적으로 빠르지만, 세션은 요청마다 서버에서 처리해야 하므로 비교적 느림

## 참고자료

[cookie-and-session](https://doooyeon.github.io/2018/09/10/cookie-and-session.html)
