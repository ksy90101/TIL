# @GeneratedValue에 대해 알아보자

> PK를 나타내기 위해 `@Id` 어노테이션을 사용하며, 생성 전략을 정의하기 위해 `@GeneratedValue` 를 사용한다. 해당 어노테이션에 대해서 알아보도록 하자.

## @Id

```java
package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * Specifies the primary key of an entity.
 * The field or property to which the <code>Id</code> annotation is applied 
 * should be one of the following types: any Java primitive type; 
 * any primitive wrapper type; 
 * <code>String</code>; 
 * <code>java.util.Date</code>; 
 * <code>java.sql.Date</code>; 
 * <code>java.math.BigDecimal</code>;
 * <code>java.math.BigInteger</code>.
 *
 * <p>The mapped column for the primary key of the entity is assumed 
 * to be the primary key of the primary table. If no <code>Column</code> annotation 
 * is specified, the primary key column name is assumed to be the name 
 * of the primary key property or field.
 *
 * <pre>
 *   Example:
 *
 *   @Id
 *   public Long getId() { return id; }
 * </pre>
 *
 * @see Column
 * @see GeneratedValue
 *
 * @since 1.0
 */
@Target({METHOD, FIELD})
@Retention(RUNTIME)

public @interface Id {}
```

- `@GeneratedValue` 를 알아보기전에 `@Id` 어노테이션을 먼저 알아보자.

> 기본키(PK)를 지정합니다. @Id 어노테이션은 기본타입, 기본 래퍼 유형, String, java.util.Date, java.sql.Date, java.math.BigDecimal, java.math.BigInteger 중 하나여야 합니다. `@Column` 어노테이션을 지정하지 않으면 열 이름은 기본 키 속성 또는 필드의 이름으로 가정합니다.

- 원래는 getter에 어노테이션을 붙일수 있었지만, 필드에도 붙여도 상관없다.
- `@GeneratedValue` 없이 `@Id` 어노테이션만 사용한다면 직접 할당이 됩니다.
- 즉, `persist()` 메서드를 호출하기 전에 애플리케이션에서 직접 실별자 값을 할당해야 하며, 식별자 값이 없을 경우 에러를 발생시킵니다.

### 예제

```java
package com.example.jwptodolist.todo.domain;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "todos")
public class Todo {

    @Id
    private Long id;

    protected Todo() {
    }

    public Todo(final Long id) {
        this.id = id;
    }

    public Long getId() {
        return id;
    }
}
```

```java
package com.example.jwptodolist.todo.domain;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @DisplayName("@Id만 있을때 저장 테스트")
    @Test
    void idAnnotationTest() {
        final Todo todo = new Todo(null);

        final Todo save = todoRepository.save(todo);

        assertThat(save.getId()).isNotNull();
    }
}
```

- 해당 테스트를 실행한다면 아래와 같이 에러가 발생한다.

> Caused by: org.hibernate.id.IdentifierGenerationException: ids for this class must be manually assigned before calling save(): com.example.jwptodolist.todo.domain.Todo
at org.hibernate.id.Assigned.generate(Assigned.java:33)

- 여기서 `IdentifierGenerationException`  을 알아보면 아래와 같이 주석으로 작성되어 있다.

> Thrown by IdentifierGenerator implementation class when ID generation fails. (ID 생성을 하려고 할때 실패해서 던져진다.)

- 그렇다면 Id를 지정하면 어떻게 될까?

```java
package com.example.jwptodolist.todo.domain;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @DisplayName("@Id만 있을때 저장 테스트")
    @Test
    void idAnnotationTest() {
        final Todo todo = new Todo(1L);

        final Todo save = todoRepository.save(todo);

        assertThat(save.getId()).isNotNull();
    }
}
```

- 해당 테스트가 정상적으로 통과되는걸 확인할 수 있습니다.
- 이게 바로 직접 할당이라고 생각하면 된다.

## @GeneratedValue에 대해 알아보자.

```java
package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
import static javax.persistence.GenerationType.AUTO;

/**
 * Provides for the specification of generation strategies for the
 * values of primary keys. 
 *
 * <p> The <code>GeneratedValue</code> annotation
 * may be applied to a primary key property or field of an entity or
 * mapped superclass in conjunction with the {@link Id} annotation.
 * The use of the <code>GeneratedValue</code> annotation is only
 * required to be supported for simple primary keys.  Use of the
 * <code>GeneratedValue</code> annotation is not supported for derived
 * primary keys.
 *
 * <pre>
 *
 *     Example 1:
 *
 *     @Id
 *     @GeneratedValue(strategy=SEQUENCE, generator="CUST_SEQ")
 *     @Column(name="CUST_ID")
 *     public Long getId() { return id; }
 *
 *     Example 2:
 *
 *     @Id
 *     @GeneratedValue(strategy=TABLE, generator="CUST_GEN")
 *     @Column(name="CUST_ID")
 *     Long id;
 * </pre>
 *
 * @see Id
 * @see TableGenerator
 * @see SequenceGenerator
 *
 * @since 1.0
 */
@Target({METHOD, FIELD})
@Retention(RUNTIME)

public @interface GeneratedValue {

    /**
     * (Optional) The primary key generation strategy
     * that the persistence provider must use to
     * generate the annotated entity primary key.
     */
    GenerationType strategy() default AUTO;

    /**
     * (Optional) The name of the primary key generator
     * to use as specified in the {@link SequenceGenerator} 
     * or {@link TableGenerator} annotation.
     * <p> Defaults to the id generator supplied by persistence provider.
     */
    String generator() default "";
}
```

> 기본키(PK) 값에 대한 생성 전략을 제공합니다. `@Id` 와 함께 엔티티 또는 매핑된 슈퍼클래스의 기본 키 속성 또는 필드에 적용할 수 있습니다.

- strategy

> (Optional) 엔티티의 기본키 생성 전략입니다. 기본값은 AUTO 입니다.

- generator

> (Optional) SequenceGenerator, TableGenerator에 지정된 대로 사용할 기본 키 생성기 이름을 지정하는 곳입니다. 기본값은 persistence가 제공하는 ID 생성기 입니다.

## GenerationType에 대해 알아보자.

- 위에서 엔티티의 기본키 생성 전략이라고 했습니다. 그럼 각 전략이 무엇이 있는지 알아봅시다.

```java
package javax.persistence;

/**
 * Defines the types of primary key generation strategies.
 *
 *@seeGeneratedValue
*
 *@since1.0
 */
public enum GenerationType {

/**
     * Indicates that the persistence provider must assign
     * primary keys for the entity using an underlying
     * database table to ensure uniqueness.
     */
TABLE,

/**
     * Indicates that the persistence provider must assign
     * primary keys for the entity using a database sequence.
     */
SEQUENCE,

/**
     * Indicates that the persistence provider must assign
     * primary keys for the entity using a database identity column.
     */
IDENTITY,

/**
     * Indicates that the persistence provider should pick an
     * appropriate strategy for the particular database. The
     *<code>AUTO</code>generation strategy may expect a database
     * resource to exist, or it may attempt to create one. A vendor
     * may provide documentation on how to create such resources
     * in the event that it does not support schema generation
     * or cannot create the schema resource at runtime.
     */
AUTO
}

```

### TABLE

> 데이터베이스에 키 생성 전용 테이블을 하나 만들고 이를 사용하여 기본키를 생성한다.

```java
package com.example.jwptodolist.todo.domain;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "todos")
public class Todo {

    @Id
		@GeneratedValue(strategy = GenerationType.TABLE)
    private Long id;

    protected Todo() {
    }

    public Todo(final Long id) {
        this.id = id;
    }

    public Long getId() {
        return id;
    }
}
```

```java
package com.example.jwptodolist.todo.domain;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @DisplayName("@Id만 있을때 저장 테스트")
    @Test
    void idAnnotationTest() {
        final Todo todo = new Todo(null);

        final Todo save = todoRepository.save(todo);

        assertThat(save.getId()).isEqaulTo(1L);
    }
}
```

```sql
create table hibernate_sequences (
       sequence_name varchar(255) not null,
        next_val bigint,
        primary key (sequence_name)
    )

create table todos (
       id bigint not null,
        content varchar(255),
        create_at datetime,
        status varchar(255),
        update_at datetime,
        primary key (id)
    )
```

### SEQUENCE

> 데이터베이스의 특별한 오브젝트 시퀀스를 사용하여 기본키를 생성한다.

- DB Sequence란 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트
- 테이블 마다 시퀀스 오브젝트를 따로 관리하고 싶으면 @SequenceGenerator에 sequenceName 속성을 추가한다.
- 이 전략은 스퀀스를 지원하는 오라클, PostgreSQL, DB@, H2 데이터베이스에서 사용할 수 있습니다.\
- 시퀀스를 생성해야 하는데 아래와 같이 생성하면 된다.

```java
CREATE SEQUENCE TODO_SEQ START WITH 1 INCREMENT BY 1;
```

```java
package com.example.jwptodolist.todo.domain;

import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "todos")
@SequenceGenerator(
        name = "TODO_SEQ_GENERATOR",
        sequenceName = "TODO_SEQ",
        initialValue = 1,
        allocationSize = 1)
public class Todo {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "TODO_SEQ_GENERATOR")
    private Long id;

		....

    protected Todo() {
    }

    public Todo(final Long id) {
        this.id = id;

    }

    public Long getId() {
        return id;
    }
}
```

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

class TodoTest {

    @DisplayName("Todo 생성자 테스트")
    @Test
    void constructorTest() {
        final Todo todo = new Todo(null);

        assertThat(todo.getId()).isEqualTo(1L);
    }
}
```

cf) 간단하게 살펴 보는 `@SequenceGenerator`

- name : 식별자 생성기 이름이며 필수로 작성
- sequnceName : 데이터베이스에 등록되어 있는 시퀀스 이름으로 기본값은 hibernate_sequence 입니다.
- initialValue : DDL 생성 시에만 사용되며 DDL을 생성할 때 처음 시작하는 수로 기본값은 1입니다.
- allocationSize : 시퀀스 한 번 호출에 증가하는 수로 기본값은 50입니다.
- catalog, schema: 데이터베이스 catelog, schema 이름
- 매핑할 DDL

```java
CREATE SEQUNCE [sequenceName] start with [initialValue] increment by [allocationSize]
```

### IDENTITY

> 기본키 생성을 데이터베이스에 위임한다. 예를 들어 MySQL의 경우 AUTO_INCREMENT를 사용하여 기본키를 생성한다.

```java
package com.example.jwptodolist.todo.domain;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "todos")
public class Todo {

    @Id
		@GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    protected Todo() {
    }

    public Todo(final Long id) {
        this.id = id;
    }

    public Long getId() {
        return id;
    }
}
```

```java
package com.example.jwptodolist.todo.domain;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @DisplayName("@Id만 있을때 저장 테스트")
    @Test
    void idAnnotationTest() {
        final Todo todo = new Todo(null);

        final Todo save = todoRepository.save(todo);

        assertThat(save.getId()).isEqaulTo(1L);
    }
}
```

```sql
create table todos (
       id bigint not null auto_increment,
        content varchar(255),
        create_at datetime,
        status varchar(255),
        update_at datetime,
        primary key (id)
    )
```

### AUTO

> JPA 구현체가 자동으로 생성 전략을 결정한다.

![image](https://user-images.githubusercontent.com/53366407/134931996-c39e227b-349b-4d3b-bb70-6e6b845d5dde.png)

- AUTO는 Data Type을 기준으로 진행합니다. 아래와 같이 Numeral이라면 `new_generator` 를 검사하는데 기본값이 TRUE이기 떄문에 SequenceStyleGenerator로 갑니다. 그 이후 MySQL이라면 sequences를 지원하지 않기 때문에 TABLE Generator로 가게 된다.
- 따라서 아래와 같은 경우에는 MySQL인 경우 TableGenerator를 사용하는걸 확인할 수 있습니다.

```java
package com.example.jwptodolist.todo.domain;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "todos")
public class Todo {

    @Id
		@GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    protected Todo() {
    }

    public Todo(final Long id) {
        this.id = id;
    }

    public Long getId() {
        return id;
    }
}
```

```java
package com.example.jwptodolist.todo.domain;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @DisplayName("@Id만 있을때 저장 테스트")
    @Test
    void idAnnotationTest() {
        final Todo todo = new Todo(null);

        final Todo save = todoRepository.save(todo);

        assertThat(save.getId()).isNotNull();
    }
}
```

```sql
create table hibernate_sequence (
       next_val bigint
    )

create table todos (
       id bigint not null,
        content varchar(255),
        create_at datetime,
        status varchar(255),
        update_at datetime,
        primary key (id)
    )
```

### ID가 UUID라면?

- 그림에서 보듯이 UUID라면 UUID Generator를 사용하게 됩니다.

```java
package com.example.jwptodolist.todo.domain;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "todos")
public class Todo {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    protected Todo() {
    }

    public Todo(final UUID id,) {
        this.id = id;
    }

    public UUID getId() {
        return id;
    }
}
```

```java
package com.example.jwptodolist.todo.domain;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.time.LocalDateTime;
import java.util.UUID;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @DisplayName("@Id만 있을때 저장 테스트")
    @Test
    void idAnnotationTest() {
        final Todo todo = new Todo(null);

        final Todo save = todoRepository.save(todo);

        assertThat(save.getId()).isInstanceOf(UUID.class);
    }
}
```
