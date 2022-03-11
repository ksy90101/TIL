# 1주차 미션 정리

# 서론

- ***[[스터디/12기] 단순 CRUD는 그만! 웹 백엔드 시스템 구현(Spring Boot)](https://programmers.co.kr/learn/courses/13469) 이 스터디를 참여해서 쓴 글입니다.**
- 1주차 미션에 대한 내용은 아래와 같습니다.
    - 간단한 CRUD 만들어보기
- 1주차 미션에서 적용했던 부분과 배웠던 부분에 대해서 이야기를 해보려고 합니다.

## 기술 스택

- Java 11
- Maven
- Spring Boot 2.6.3
- Spring Web
- Spring JDBC
- Spring Validation

## 배운 점

### Email VO 분리

- User 도메인에 있는 email은 validation이 필요하다. 이것은 User의 책이밍라기 보단 Email 객체의 책임이라고 생각이 들었다. 따라서 해당 필드를 VO로 분리하였다.

```java
public class Email {
    private static final String REGEX = "^[a-zA-Z0-9_!#$%&’*+/=?`{|}~^.-]+@[a-zA-Z0-9.-]+$";
    private static final Pattern pattern = Pattern.compile(REGEX);

    private final String email;

    public Email(String email) {
        validate(email);
        this.email = email;
    }

    public String getEmail() {
        return email;
    }

    private void validate(String value) {
        var matcher = pattern.matcher(value);

        if (!matcher.matches()) {
            throw new IllegalArgumentException(UserExceptionMessage.IN_VALID_EMAIL.getMessage());
        }
    }
}
```

### final을 사용해서 불변을 유지하자

- 위에 email처럼, 객체(인스턴스)가 생성되고 변경될 여지가 없다면 fianl으로 해당 필드를 불변으로 유지하는것이 좋다.

### Database Column 명을 Enum으로 관리해보자.

- 이건 미션을 하멶서 JDBC를 사용하다보니, 매번 Repository의 로직을 만들면서 컬럼명을 문자열로 작성해줘야 했었는데, 이걸 이번에 enum으로 관리해봤다.

```java
package com.github.prgrms.socialserver.user.column;

public enum UserColumn {
    SEQ("seq"),
    EMAIL("email"),
    PASSWORD("passwd"),
    LOGIN_COUNT("login_count"),
    LAST_LOGIN_AT("last_login_at"),
    CREATE_AT("create_at");

    private final String name;

    UserColumn(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

- 장점은 휴먼에러가 없다는 것이다. 단점은 관리해야 하는 포인트가 하나 더 들어났다는 점인데, 이건 이게 없어도 똑같을거 같다는 생각이 들었다.

### ApiResponse라는 객체를 만들어서 Body를 관리해보자.

```java
package com.github.prgrms.socialserver.common;

public class ApiResponse<T> {
    private final boolean success;
    private final T response;

    private ApiResponse(boolean success, T response) {
        this.success = success;
        this.response = response;
    }

    public static <T> ApiResponse<T> ofSuccess(T response) {
        return new ApiResponse<>(true, response);
    }

    public static <T> ApiResponse<T> ofFail(T response) {
        return new ApiResponse<>(false, response);
    }

    public boolean isSuccess() {
        return success;
    }

    public T getResponse() {
        return response;
    }
}
```

- 사실 이것과 같이 ResponseEnttiy를 사용했는데, 이러한 리뷰를 남겨주셨다.

<img width="752" alt="image" src="https://user-images.githubusercontent.com/53366407/157880173-b9a44276-fbd9-4921-be21-23469ec0527b.png">

### 서비스를 쪼개자.

- 이번에는 간단한 RD는 하나의 서비스에, C는 다른 서비스로 빼서 작업을 하였는데, 서비스를 쪼개다 보니 클래스명으로 해당 클래스가 어떤 로직을 하는지 명확하게 나타낼수 있으며, 각 클래스가 작아진다고 생각이 들었다. 그러나 간단한 작업들도 다 분리한다면 관리해야 하는 포인트가 많아 진다고 생각이 들긴 한다.

### InMemoryRepository를 만들어서 테스트 하자.

```java
package com.github.prgrms.socialserver.mock;

import com.github.prgrms.socialserver.user.domain.User;
import com.github.prgrms.socialserver.user.repository.UserRepository;

import java.time.LocalDateTime;
import java.util.*;

public class UserInMemoryRepository implements UserRepository {
    private static final Map<Long, User> users = new HashMap<>();
    private static Long seq = 0L;

    public User insert(User user) {
        var selectUser = new User(seq, user.getEmail(), user.getPassword(), 0, null, LocalDateTime.now());

        users.put(seq, selectUser);

        autoIncrement();

        return selectUser;
    }

    public Optional<User> findBySeq(Long seq) {
        return Optional.ofNullable(users.get(seq));
    }

    public List<User> findAll() {
        return new ArrayList<>(users.values());
    }

    @Override
    public Optional<User> findByEmail(String email) {
        return users.values()
                .stream()
                .filter(user -> user.getEmail().equals(email))
                .findFirst();
    }

    public void clear() {
        users.clear();
    }

    private void autoIncrement() {
        seq++;
    }
}
```

- 위 코드와 같이 inMemoryRepository를 만들어서 작업을 하였는데, 역시 RDB와 다르게 구현되는 케이스도 많았다고 생각이 든다. 그래도 이러한 시도를 하면서 느낀점도 있어서 좋은거 같지만, 관리해야 하고, 리소스가 조금 많이 드는 단점이 있었다. 그래도 이러한 시도를 해봤다는 것에 의미를 두었다.

## 느낀점

- 테스트 코드를 좀 더 꼼꼼히 짰으면 어떨까 라는 생각이 들었다.
- 여러가지 시도를 했지만, 기본 구현에 충실하지 못했던거 같다.
- 해리쌤이 `각 필드에 대한 정책에 대해서 왜 아무도 물어보지 않죠?` 라는 말을 해주셨다. 여기서 조금 충격이였는데, 기능에만 집중하다 보니 이러한 꼼꼼한 부분을 챙기지 못한다고 생각이 들어서, 다음에는 이러한 부분들을 챙겨봐야 겟다고 생각이 들었다 :)
