# SpringBootTest webEnvironment

## DEFINED_PROT

- 지정된 포트로 실제 서블릿 컨테이너를 띄운다.
- 이때 PORT의 값은 application.properties에 설정된 값 또는 기본값인 8080이다.)

## MOCK(기본값)

- 실제 서블릿 컨테이너를 띄우지 않고 서블릿 컨테이너를 mocking해서 실행된다.
- 이때 속성값을 사용할때는 보통 MockMvc를 주입받아 테스트한다.
- 서블릿 API들이 classpath가 없다면 ApplicationContext를 생성한다.
- MockMvc 기반의 어플리케이션 테스트를 위해 `@AutoConfigureMockMvc`의 주입과 함께 사용된다.

## NONE

- ApplicationContext가 로드되지만 실제 서블릿 컨테이너를 동작시키지 않는다.

## RANDOM_PORT

- 실제로 테스트를 위한 서블릿 컨테이너를 띄우며, PORT를 랜덤으로 지정한다.
