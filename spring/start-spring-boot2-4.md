# 4장. 스프링 부트 

> [처음배우는 스프링 부트 2](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=168752840)
> 
> [예제코드](https://github.com/ksy90101/book-spring-boot-start)

## 4.1. 커뮤니티 게시판 설계하기

- MVC(Model-View-Controller) 패턴

클라이언트 → 컨트롤러 ↔ 서비스 ↔리포지토리 ↔DB

　 　 　 　 　 ⬇️             
클라이언트 ← 타임리프 

- 클라이언트가 서버에 데이터를 요청하면 서버가 데이터를 제공
- 데이터를 요청받은 서버는 타임리프를 사용하여 뷰를 구성해 보여줌
    - 타임리프란? 웹 또는 독립적인 실행 환경에서 사용되는 자바 서버 사이드 템플릿 엔진.
    - 스프링 부트의 공식적인 템플릿 엔진으로 starter를 지원함.

- 엔티티 관계도
    - Board
        - id(PK)
        - title
        - content
        - board_type
        - created_date
        - updated_date
        - user_id(FK)
    - User
        - id(PK)
        - name
        - password
        - email
        - created_date
        - updated_date

    - Board와 User는 1:1 관계

## 4.2. 커뮤니티 게시판 프로젝트 준비하기

- 의존성 추가

![start-spring-boot2-4-1](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-4-1.png?raw=true)

## 4.3. 커뮤니티 게시판 구현하기

### 4.3.1. 프로젝트 의존성 구성

- build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.2.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'java'
}

group = 'spring-boot-web'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
}

test {
    useJUnitPlatform()
}
```

- plugins : 필요한 플러그인을 정의하고 사용한다.
- io.spring.dependency-management : 종속성 관리 플러그인.
- dependencies : 프로젝트 내 사용할 라이브러리의 의존성들을 정의합니다. 의존성 관리 플러그인에 의해 2.3.2와 호환되는 버전을 가져옵니다.

- Spring-Boot-Community-Web 디렉토리 구조

![start-spring-boot2-4-2](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-4-2.png?raw=true)

### 4.3.2. 스프링 부트 웹 스타터 살펴보기

![start-spring-boot2-4-3](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-4-3.png?raw=true)

- Spring-boot-starter
    - 스프링 부트 시작하는 기본 설정
- Spring-boot-starter-tomcat : 내장 톰켓
- spring-boot-starter-json : jackson 라이브러리를 지원해준다. JSON 데이터형의 파싱, 데이터 바인딩 함수 등을 제공
- spring-web : HTTP Integration, Servlet filters, Spring HTTP invoker 및 HTTP Core ....
- spring-webmvc : request를 전달하는 MVC로 디자인된 DispatcherServelt 기반의 라이브러리

### 4.3.3. 도메인 매핑하기

- 도메인 매핑이란 JPA를 이용하여 DB와 도메인 클래스를 연결해주는 작업
- JPA를 활용한 H2 DB 매핑 구성도

![start-spring-boot2-4-4](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-4-4.png?raw=true)

- Repository란?
    - 스프링이 관리하는 컴포넌트에서 퍼시스턴스 계층에 대해 더 명확하게 명시하는 특수한 제네릭 스테레오타입을 말한다.
    - 여기서 퍼시스턴스 계층이란 물리적 저장공간을 뜻함
    - 영속성을 가진 파일이나 DB에 로직을 구현하는 것

### BoardType

- 게시판 종류

```java
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public enum BoardType {
    NOTICE("공지사항"),
    FREE("자유게시판");

    private final String value;
}
```

### Board

- 게시판 entity

```java
import java.io.Serializable;
import java.time.LocalDateTime;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToOne;
import javax.persistence.Table;

import lombok.AccessLevel;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import spring.boot.community.domain.enums.BoardType;

@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
@Table(name = "BOARD")
public class Board implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column
    private String title;

    @Column
    private String subTitle;

    @Column
    private String content;

    @Column
    @Enumerated(EnumType.STRING)
    private BoardType boardType;

    @Column
    private LocalDateTime createdDate;

    @Column
    private LocalDateTime updatedDate;

    @OneToOne(fetch = FetchType.LAZY)
    private User user;

    @Builder
    public Board(
            final String title, final String subTitle, final String content, final BoardType boardType,
            final LocalDateTime createdDate,
            final LocalDateTime updatedDate, final User user) {
        this.title = title;
        this.subTitle = subTitle;
        this.content = content;
        this.boardType = boardType;
        this.createdDate = createdDate;
        this.updatedDate = updatedDate;
        this.user = user;
    }
}
```

- @GeneratedValue(Strategy = GenerationType.IDENTITY) : 기본 키가 자동으로 할당되도록 설정
- @Enumerated(EnumType.STRING) : Enum 타입 매핑용 어노테이션. enum형과 데이터베이스 데이터 변환을 지원
- @OneToOne(fetch = FetchType.LAZY) : Board와 User를 1:1 관계로 설정. 실제 DB에는 User가 저장되는 것이 아니라 User의 PK가 FK로 저장된다.
    - fetch
        - eager : 처음 도메인을 조회할 때 즉시 관련 객체를 조회
        - lazy : 객체를 조회하는 시점이 아닌 객체가 실제 사용될 때 조회

### BoardRepository

```java
import java.util.Optional;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface BoardRepository extends JpaRepository<Board, Long> {
    Optional<Board> findByUser(User user);
}
```

### User

```java
import java.time.LocalDateTime;

import javax.persistence.Column;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

import lombok.AccessLevel;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity
@Table(name = "USER")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column
    private String name;

    @Column
    private String email;

    @Column
    private String password;

    @Column
    private LocalDateTime createdDate;

    @Column
    private LocalDateTime updatedDate;

    @Builder
    public User(
            final String name, final String email, final String password, final LocalDateTime createdDate,
            final LocalDateTime updatedDate) {
        this.name = name;
        this.email = email;
        this.password = password;
        this.createdDate = createdDate;
        this.updatedDate = updatedDate;
    }
}
```

### UserRepository

```java
import java.util.Optional;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

### 4.3.4.도메인 테스트하기

- @DateJpaTest를 사용해 도메인 테스트
- JPA 테스트를 지원하는 어노테이션으로 테스트 시 실행된 변경사항이 실제 DB에 반영되지 않는다.
- 테스트 수행 후 테스트 이전으로 롤백하기 때문이다.

### UserRepositoryTest

- 유저를 저장하고 email을 이용해 user를 찾아오는지 확인하는 테스트 작성

```java
import static org.assertj.core.api.Assertions.*;

import java.time.LocalDateTime;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    private User user;

    @BeforeEach
    void setUp() {
        user = userRepository.save(User.builder()
                .name("rutgo")
                .password("test")
                .email("rutgo@gmail.com")
                .createdDate(LocalDateTime.now())
                .build());
    }

    @DisplayName("findByEmail() 메소드 테스트")
    @Test
    void findByEmailTest() {
        final User expected = userRepository.findByEmail("rutgo@gmail.com")
                .orElseThrow(() -> new IllegalArgumentException("해당 유저를 찾을수 없습니다."));

        assertThat(expected).isEqualTo(user);
    }
}
```

- @DataJpaTest : 스프링 부트 JPA 테스트를 위한 전용 어노테이션. 첫 설계 시 엔티티 간의 관계 설정 및 기능 테스트를 가능하게 도와줌. 테스트가 끝날 때마다 자동 롤백해줍니다.
- @BeforeEach : 각 테스트가 실행되기 전에 실행될 메서드를 선언. Junit4에서는 @Before를 사용

### BoardRepositoryTest

- 게시물을 생성하고 user를 이용해 해당 게시물을 가져오는지 확인하는 테스트

```java
import static org.assertj.core.api.Assertions.*;

import java.time.LocalDateTime;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import spring.boot.community.domain.enums.BoardType;

@DataJpaTest
class BoardRepositoryTest {
    private final static String BOARD_TEST_TITLE = "타이틀";

    @Autowired
    private BoardRepository boardRepository;

    @Autowired
    private UserRepository userRepository;

    @BeforeEach
    void setUp() {
        final User user = userRepository.save(User.builder()
                .name("rutgo")
                .password("test")
                .email("rutgo@gmail.com")
                .createdDate(LocalDateTime.now())
                .build());

        boardRepository.save(Board.builder()
                .title("타이틀")
                .subTitle("서브 타이틀")
                .content("콘텐츠")
                .boardType(BoardType.FREE)
                .createdDate(LocalDateTime.now())
                .updatedDate(LocalDateTime.now())
                .user(user)
                .build());
    }

    @DisplayName("findTest() 찾기 메소드 테스트")
    @Test
    void findTest() {
        final User user = userRepository.findByEmail("rutgo@gmail.com")
                .orElseThrow(() -> new IllegalArgumentException("해당 유저를 찾을수 없습니다."));
        final Board board = boardRepository.findByUser(user)
                .orElseThrow(() -> new IllegalArgumentException("해당 게시글을 찾을 수 없습니다."));

        assertThat(board.getTitle()).isEqualTo(BOARD_TEST_TITLE);
        assertThat(board.getUser()).isEqualTo(user);
    }
}
```

### BoardService

```java
import java.util.NoSuchElementException;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

import lombok.RequiredArgsConstructor;
import spring.boot.community.domain.Board;
import spring.boot.community.domain.BoardRepository;

@RequiredArgsConstructor
@Service
public class BoardService {
    private final BoardRepository boardRepository;

    public Page<Board> findBoardList(Pageable pageable) {
        pageable = PageRequest.of(
                pageable.getPageNumber() <= 0 ? 0 : pageable.getPageNumber() - 1, pageable.getPageSize()
        );

        return boardRepository.findAll(pageable);
    }

    public Board findBoardById(final Long id) {
        return boardRepository.findById(id)
                .orElseThrow(() -> new NoSuchElementException("해당 게시물을 찾을 수 없습니다."));
    }
}
```

- @service : 서비스 계층을 명시하는 특수한 제네릭 스테레오 형식
- pageable : pageable로 넘어온 pageNumber 객체가 0 이하일 때 0으로 초기화. 기본 페이지 크기인 10으로 새로운 PageRequest 객체를 만들어 페이징 처리된 게시글 리스트 반환

### BoardController

```java
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import lombok.RequiredArgsConstructor;
import spring.boot.community.service.BoardService;

@RequiredArgsConstructor
@Controller
@RequestMapping("/board")
public class BoardController {
    private final BoardService boardService;

    @GetMapping({"", "/"})
    public String board(@RequestParam(defaultValue = "0") final Long id, final Model model) {
        model.addAttribute("board", boardService.findBoardById(id));

        return "/board/form";
    }

    @GetMapping("/list")
    public String list(@PageableDefault final Pageable pageable, final Model model) {
        model.addAttribute("boards", boardService.findBoardList(pageable));

        return "board/list";
    }
}
```

- @ReqeustMapping : API URI 경로 설정
- @GetMapping : 매핑 경로를 중괄호로 사용하면 여러 개 받을 수 있다.
- @RequestParam : id를 필수로 받는다. 만약 바인딩할 값이 없으면 기본 값 0으로 설정.
- @PageableDefault : size, sort, direction 등을 사용하여 페이징 처리 규약을 정의 함.
- src/resurces/templates기준으로 데이터 바인딩할 뷰 경로 지정한 것을 return 함

### 4.3.5. CommandLineRunner를 사용하여 DB에 데이터 넣기

- CommandLineRunner는 애플리케이션 구동 후 특정 코드를 실행하고 싶을 때 직접 구현하는 인터페이스
- 애플리케이션 구동 시 테스트 데이터를 함께 생성하여 데모 프로젝트를 실행/테스트 할 때 편리함.
- 또한, 여러 CommandLineRunner를 구현하여 같은 애플리케이션 컨텍스트의 빈에 사용할 수 있음.

- CommunityApplication

```java
import java.time.LocalDateTime;
import java.util.stream.IntStream;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import spring.boot.community.domain.Board;
import spring.boot.community.domain.BoardRepository;
import spring.boot.community.domain.User;
import spring.boot.community.domain.UserRepository;
import spring.boot.community.domain.enums.BoardType;

@SpringBootApplication
public class CommunityApplication {
    public static void main(final String[] args) {
        SpringApplication.run(CommunityApplication.class, args);
    }

    @Bean
    public CommandLineRunner runner(final UserRepository userRepository, final BoardRepository boardRepository) {
        return (args -> {
            final User user = userRepository.save(User.builder()
                    .name("rutgo")
                    .password("test")
                    .email("rutgo@gmail.com")
                    .createdDate(LocalDateTime.now())
                    .build());

            IntStream.rangeClosed(1, 200).forEach(index ->
                    boardRepository.save(Board.builder()
                            .title("타이틀")
                            .subTitle("서브 타이틀")
                            .content("콘텐츠")
                            .boardType(BoardType.FREE)
                            .createdDate(LocalDateTime.now())
                            .updatedDate(LocalDateTime.now())
                            .user(user)
                            .build()));
        });
    }
}
```

- 스프링은 빈으로 생성된 메서드를 파라미터로 DI 시키는 메커니즘이 존재한다. 생성자를 통해 주입하는 방법과 유사하다.
- DI(Dependency Injection) : 스프링 주요 틍성 중 하나로 주로 의존 관계 주입이라고 하며, 의존 관계를 주입하는 게 아니라 단지 객체의 레퍼런스를 전달하여 참조시킨다는 의미로 의존 관계 설정이라고 한다.
- 빌더 패턴 : 객체 생성 과정과 표현 방법을 분리하여 객체 단계별 동일한 생성 절차로 복잡한 객체로 만드는 패턴

### 4.3.6. 게시글 리스트 기능 만들기

- 뷰를 구성하는 데 다양한 서버 사이드 템플릿 엔진을 사용할 수 있다. 타임리프, 프리마커, 무스타치 등 템플릿 엔진이 있다.

- 서버 사이드 템플릿 이란?
    - 미리 정의된 HTML에 데이터를 반영하여 뷰를 만드는 작업을 서버에서 진행하고 클라이언트에 전달하는 방식
    - JSP, 타임리프 등 서버 사이드 템플릿 엔진이며, 스프링 부트 2.0에서 지원하는 것은 타임리프, 프리마커, 무스타치, 그루비 등이 있다.

### list.html

```html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0"
          name="viewport">
    <meta content="ie=edge" http-equiv="X-UA-Compatible">
    <title>Board Form</title>
    <link rel="stylesheet" th:href="@{/css/base.css}">
    <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
</head>
<body>

<div th:replace="layout/header::header"></div>
<div class="container">
    <div class="page-header">
        <h1>게시글 목록</h1>
    </div>
    <div class="pull-right" style="width:100px; margin:10px 0;">
        <a class="btn btn-primary btn-block" href="/board">등록</a>
    </div>

    <br/>
    <br/>
    <br/>

    <div id="mainHide">
        <table class="table table-hover">
            <thead>
            <tr>
                <th class="col-md-1">#</th>
                <th class="col-md-2">서비스 분류</th>
                <th class="col-md-5">제목</th>
                <th class="col-md-2">작성 날짜</th>
                <th class="col-md-2">수정 날짜</th>
            </tr>
            </thead>

            <tbody>
            <tr th:each="board : ${boards}">
                <td th:text="${board.id}">
                </td>

                <td th:text="${board.boardType.value}"></td>
                <td>
                    <a th:href="'/board?id='+${board.id}" th:text="${board.title}"></a>
                </td>
                <td th:text="${#temporals.format(board.createdDate, 'yyyy-MM-dd HH:mm')}"></td>
                <td th:text="${#temporals.format(board.updatedDate, 'yyyy-MM-dd HH:mm')}"></td>
            </tr>
            </tbody>
        </table>
    </div>

    <nav aria-label="Page navigation" style="text-align: center;">
        <ul class="pagination"
            th:with="startNumber=${T(Math).floor(boards.number/10)} * 10 + 1,
            endNumber=(${boards.totalPages} > ${startNumber} + 9) ?
            ${startNumber} + 9 : ${boards.totalPages}">
            <li><a href="/board/list?page=1">&laquo;</a></li>
            <li th:style="${boards.first} ? 'display:none'">
                <a th:href="@{/board/list(page=${boards.number})}">&lsaquo;</a>
            </li>
            <li th:class="(${page} == ${boards.number} + 1) ? 'active'"
                th:each="page : ${#numbers.sequence(startNumber, endNumber)}">
                <a th:href="@{/board/list(page=${page})}"
                   th:text="${page}">
                    <span class="sr-only"></span>
                </a>
            </li>

            <li th:style="${boards.last} ? 'display:none'">
                <a th:href="@{/board/list(page=${boards.number} + 2)}">&rsaquo;</a>
            </li>
            <li>
                <a th:href="@{/board/list(page=${boards.totalPages})}">&rsaquo;</a>
            </li>
        </ul>
    </nav>
</div>

<div th:replace="layout/footer::footer"></div>

</body>
</html>
```

- th란?
    - html을 효과적으로 대체하는 네임스페이스로 th:test 프로퍼티와 함께 사용하면 내부에 표현된 #{...} 구문을 실제 값으로 대체
- @{...}는 타임리프의 기본 링크 표현 구문으로 server-relative URL 방식. 즉, 동일 서버 내의 다른 컨텍스트로 연결해주는 방식으로 서버 루트 경로를 기준으로 구문에서 경로를 탐색하여 href=URL을 대체
- th:each란?
    - 반복 구문
    - board 객체에 담긴 getter 메서드를 `board.`으로 접근이 가능하다.

### 4.3.7. 타임리프 자바 8 날짜 포맷 라이브러리 추가

- temporals.format 함수를 사용하여 날짜 포맷 변환을 수행
- LocalDateTime의 기본형은 ISO 방식으로 출력이 된다.
- 따라서 일반 날짜 표기 형식으로 변환은 직접 포매팅 메서드를 만들거나 라이브러리를 사용하면 된다.
- temporals를 사용할 수 있게 해주는 thymeleaf-extras-java8time 의존성은 spring-boot-starter-thymeleaf 스타터에 포함되어 있다.

- 주요 날짜 포매팅 함수
    - ${#temporals.format(temporal, 'yyyy/MM/dd HH:mm')}
    - ${#temporals.arrayFormat(temporalsArray, 'yyyy/MM/dd HH:mm')}
    - ${#temporals.listFromat(temporalsList, 'yyyy/MM/dd HH:mm')}
    - ${#temporals.setFormat(temporalsSet, 'yyyy/MM/dd HH:mm')}

### 4.3.8. 페이징 처리하기

- th:with
    - ul 태그 안에서 사용할 변수를 정의한다.
    - startNumber와 endNumber 변수로 페이지의 처음과 끝을 동적으로 계산하여 초기화
- 각 페이지 버튼은 th:each를 사용하여 startNumber부터 endNumber까지 출력시킨다.
- pageable 객체는 편리하게도 해당 페이지가 처임인지 마지막인지 데이터(boolean)를 제공한다.

- 결과 확인

![start-spring-boot2-4-5](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-4-5.png?raw=true)

### 4.3.9 작성 폼 만들기

### form.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta content="text/html; charset=UTF-8" http-equiv="Content-Type"/>
    <title>Board Form</title>
    <link rel="stylesheet" th:href="@{/css/base.css}"/>
    <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}"/>
</head>
<body>
<div th:replace="layout/header::header"></div>

<div class="container">
    <div class="page-header">
        <h1>게시글 등록</h1>
    </div>
    <br/>
    <input id="board_id" th:value="${board?.id}" type="hidden"/>
    <input id="board_create_date" th:value="${board?.createdDate}" type="hidden"/>
    <table class="table">
        <tr>
            <th style="padding:13px 0 0 15px">게시판 선택</th>
            <td>
                <div class="pull-left">
                    <select class="form-control input-sm" id="board_type">
                        <option>--분류--</option>
                        <option th:selected="${board?.boardType?.name() == 'notice'}" th:value="notice">공지사항</option>
                        <option th:selected="${board?.boardType?.name() == 'free'}" th:value="free">자유게시판</option>
                    </select>
                </div>
            </td>
        </tr>
        <tr>
            <th style="padding:13px 0 0 15px;">생성날짜</th>
            <td><input class="col-md-1 form-control input-sm" readonly="readonly"
                       th:value="${board?.createdDate} ? ${#temporals.format(board.createdDate,'yyyy-MM-dd HH:mm')} : ${board?.createdDate}"
                       type="text"/>
            </td>
        </tr>
        <tr>
            <th style="padding:13px 0 0 15px;">제목</th>
            <td><input class="col-md-1 form-control input-sm" id="board_title" th:value="${board?.title}" type="text"/>
            </td>
        </tr>
        <tr>
            <th style="padding:13px 0 0 15px;">부제목</th>
            <td><input class="col-md-1 form-control input-sm" id="board_sub_title" th:value="${board?.subTitle}"
                       type="text"/></td>
        </tr>
        <tr>
            <th style="padding:13px 0 0 15px;">내용</th>
            <td><textarea class="col-md-1 form-control input-sm" id="board_content" maxlength="140" rows="7"
                          style="height: 200px;"
                          th:text="${board?.content}"></textarea><span class="help-block"></span>
            </td>
        </tr>
        <tr>
            <td></td>
            <td></td>
        </tr>
    </table>
    <div class="pull-left">
        <a class="btn btn-default" href="/board/list">목록으로</a>
    </div>
    <div class="pull-right">
        <button class="btn btn-primary" id="insert" th:if="!${board?.id}" type="button">저장</button>
        <button class="btn btn-info" id="update" th:if="${board?.id}" type="button">수정</button>
        <button class="btn btn-danger" id="delete" th:if="${board?.id}" type="button">삭제</button>
    </div>
</div>

<div th:replace="layout/footer::footer"></div>

<script th:src="@{/js/jquery.min.js}"></script>
</body>
</html>
```

- ${...?}처럼 구문 뒤에 ?를 붙여서 null 체크를 추가해 값이 null인 경우에는 빈값이 출력되도록 함.

![start-spring-boot2-4-6](https://github.com/ksy90101/TIL/blob/master/spring/img/start-spring-boot2-4-6.png?raw=true)

## 4.4. 마치며

- 스프링 부트의 autoConfiguration 기능을 사용하면 설정을 최소화 할 수 있다.
- 또한 pageable 인터페이스를 사용하면 쉽게 페이징을 할 수 있다.
