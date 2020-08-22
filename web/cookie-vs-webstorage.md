## Cookie

- 클라이언트에 대한 정보를 이용자 PC의 하드디스크에 보관하기 위해서 웹 사이트에서 클아이언트의 웹 브라우저에 전송하는 정보
- 로그인 기능 구현 시 대부분 토큰을 사용
- 이 토큰은 로그인 시 고유하게 가지고 있는 `unique`한 값을 가지게 됨
- 웹에서 로그인을 위해 항상 토큰을 발급받는 API를 사용해야 함
- 근데, 서비스를 이용하다 보면 토큰을 반드시 매개변수로 보내야 하는 경우가 있다.
- 이런 경우 토큰을 매번 생성한다면 api하나 호출하기 위해 토큰을 얻는 api를 또 호출하는 쓸데 없는 일이 반복
- 따라서 쿠키라는 곳에 저장해놓는 것이다.

## WebStorage

- HTML5에서 쿠키의 단점을 보완해서 만든 기술
- 기본적으로 key-value로 되어 있음
- 쿠키와 마찬가지로 클라이언트에 대한 정보를 저장
- 쿠키와 웹스토리지는 크게 차이는 없지만, 쿠키보다 웹스토리지가 더 효율적이라는 의견이 많다.
- 저장용량에서도 쿠키는 4kb이지만, 로컬스토리지는 약 5Mb정도 까지 가능하고 서버로 전송을 안하기 때문이다.
- 가장 큰 차이점이 존재하는데, 서버에 클라이언트에 대한 데이터를 저장하지 않은 것이다.
- 웹 스토로지는 자기 로컬영역에만 저장 해두고 key값에 해당하는 value값들을 비교, 유지 하는 형식이지만, 쿠키는 서버와 자신 로컬 영역에 저장을 해두고 비교할 떄마다 일종의 쿠키용 api를 만들어 호출하는 방식

### LocalStorage

- 클라이언트에 대한 정보를 반영구적으로 보관하는 것

### SessionStorage

- 세션이 종료되면(브라우저를 닫을 경우) 클라이언트에 대한 정보를 삭제

## 결론

- Cookie
    - 장점 : 거의 모든 브라우저에서 지원
    - 단점 : api가 한번 더 호출되므로 서버에 부담증가, 쿠키자체의 용량이 적음
- WebStorage
    - 장점 : 서버에 불필요하게 데이터 저장 안함, 용량이 큼
    - 단점 : HTML4만 지원