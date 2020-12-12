# JPA DDL Auto With Hibernate

## 서론

- DDL(Data Definition Language, 데이터 정의 언어)를 자동으로 생성해주는 속성입니다.
- CREATE - 새로운 데이터베이스 테이블, view 등을 생성
- DROP - 이미 존재하는 데이터 베이스 테이블, view 등을 제거
- ALTER - 이미 존재하는 데이터베이스 개체에 대한 변경
- 이번 글은 Hibernate를 이용해 생성해보도록 하겠습니다.
- 속성명은 `hibernate.hbm2ddl.auto` 입니다.

## 환경 세팅

```groovy
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.hibernate:hibernate-entitymanager:5.3.7.Final'
    implementation "jakarta.xml.bind:jakarta.xml.bind-api:2.3.2"
    implementation "org.glassfish.jaxb:jaxb-runtime:2.3.2"
    runtime 'com.h2database:h2:1.4.197'
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
    testImplementation 'org.assertj:assertj-core:3.18.1'

}

test {
    useJUnitPlatform()
}
```

- JDK 9 이상 부터는 xml 파싱 의존성을 추가해야 하기 때문에 위와 같은 xml 관련 의존성을 추가했습니다.
- Spring Data JPA가 아닌 hibernate만을 사용해서 설정하도록 하겠습니다.
- hibernate 속성 설정
    - resources/META-INF/persistence.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.2">

        <persistence-unit name="hello">
            <!-- <class>tacademy.jpa.basic.domain.data.Member</class>-->
            <!-- <class>tacademy.jpa.basic.domain.data.Team</class>-->
            <class>tacademy.jpa.basic.domain.obj.Member</class>
            <class>tacademy.jpa.basic.domain.obj.Team</class>

            <properties>
                <!--필수 속성-->
                <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
                <property name="javax.persistence.jdbc.user" value="sa"/>
                <property name="javax.persistence.jdbc.password" value=""/>
                <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
                <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

                <!-- 옵션 -->
                <property name="hibernate.show_sql" value="true"/>
                <property name="hibernate.format_sql" value="true"/>
                <property name="hibernate.user_sql_comments" value="true"/>

                <property name="hibernate.hbm2ddl.auto" value="create"/>
            </properties>
        </persistence-unit>
    </persistence>
    ```

- 테스트 Entity를 위해 Member를 추가합니다.

```java
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;

@Entity
public class Member {
    @Id
    private Long id;

    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;

    private String name;

    public Member() {
    }

    public Member(final Long id, final Team team, final String name) {
        this.id = id;
        this.team = team;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public Team getTeam() {
        return team;
    }

    public String getName() {
        return name;
    }
}
```

## create

- 기존 테이블 삭제 후 다시 생성하는 옵션입니다.
- DROP + CREATE
- 실제 운영 서버나 테스트 서버에서는 절대 사용하면 안됩니다. 로컬 서버에서 테스트 하는 용도로만 쓰는 걸 추천드립니다.

```xml
<property name="hibernate.hbm2ddl.auto" value="create"/>
```

- 실제 쿼리 확인

```sql
drop table Member if exists

create table Member (
       id bigint not null,
        name varchar(255),
        team_id bigint,
        primary key (id)
    )
```

## create-drop

- create와 같으나 종료 시점에 테이블을 DROP합니다.
- 실제 운영 서버나 테스트 서버에서는 절대 사용하면 안됩니다. 로컬 서버에서 테스트 하는 용도로만 쓰는 걸 추천드립니다.

```xml
<property name="hibernate.hbm2ddl.auto" value="create-drop"/>
```

- 실제 쿼리 확인

```sql
drop table Member if exists

create table Member (
       id bigint not null,
        name varchar(255),
        team_id bigint,
        primary key (id)
    )

drop table Member if exists
```

## update

- 변경분에만 반영합니다.
- 실제 운영 서버나 테스트 서버에서는 절대 사용하면 안됩니다. 로컬 서버에서 테스트 하는 용도로만 쓰는 걸 추천드립니다.
- 테이블이 있다는 조건하에 필드를 추가해보도록 하겠습니다. 가장 간단한 나이를 뜻하는 age를 만들어서 다시 실행했을때 쿼리를 확인해보도록 하겠습니다.

```xml
<property name="hibernate.hbm2ddl.auto" value="update"/>
```

- age 필드 추가

```java
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;

@Entity
public class Member {
    @Id
    private Long id;

    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;

    private String name;

    private int age;

    public Member() {
    }

    public Member(final Long id, final Team team, final String name, final int age) {
        this.id = id;
        this.team = team;
        this.name = name;
        this.age = age;
    }

    public int getAge() {
        return age;
    }

    public Long getId() {
        return id;
    }

    public Team getTeam() {
        return team;
    }

    public String getName() {
        return name;
    }
}
```

- 쿼리 확인

```sql
alter table TEST.PUBLIC.MEMBER 
       add column age integer not null
```

## validate

- 엔티티 클래스와 실제 테이블이 정상 매핑되었는지 확인합니다.
- 아까와 같이 테이블에 age가 없는 상태에서 age를 추가하고 해당 옵션을 주고 실행했을때 어떻게 되는지 보도록 하겠습니다.

```text
...

Caused by: org.hibernate.tool.schema.spi.SchemaManagementException: Schema-validation: missing column [age] in table [Member]
	at org.hibernate.tool.schema.internal.AbstractSchemaValidator.validateTable(AbstractSchemaValidator.java:136)
	at org.hibernate.tool.schema.internal.GroupedSchemaValidatorImpl.validateTables(GroupedSchemaValidatorImpl.java:42)
	at org.hibernate.tool.schema.internal.AbstractSchemaValidator.performValidation(AbstractSchemaValidator.java:89)
	at org.hibernate.tool.schema.internal.AbstractSchemaValidator.doValidation(AbstractSchemaValidator.java:68)
	at org.hibernate.tool.schema.spi.SchemaManagementToolCoordinator.performDatabaseAction(SchemaManagementToolCoordinator.java:191)
	at org.hibernate.tool.schema.spi.SchemaManagementToolCoordinator.process(SchemaManagementToolCoordinator.java:72)
	at org.hibernate.internal.SessionFactoryImpl.<init>(SessionFactoryImpl.java:310)
	at org.hibernate.boot.internal.SessionFactoryBuilderImpl.build(SessionFactoryBuilderImpl.java:467)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:939)

```

- 이와 같이 Member Table에 age가 없다는 에러를 확인할 수 있다.
- 테스트 서버에서는 사용해도 된다고 생각하지만, 실제 서비스가 커진다면 테스트 서버에서도 문제를 발생시킬 수 있습니다.

## none

- 이 속성을 아예 사용하지 않겠다는 의미입니다.

## 결론

- JPA는 편리한 기능을 제공해주지만, 실제 운영서버에서 사용한다면 위험이 있을 수 있습니다.
- 실제 DB는 DBA가 있다면 DBA에게 맡기는 것이 최고의 선택이라고 생각합니다.
- 운영 장비에서는 절대로 create, create-drop, update를 사용하면 안됩니다.
- 개발 초기 단계에서는 create 또는 update
- 테스트 서버는 update 또는 validate
- 스테이징과 운영 서버는 validate 또는 none

## 참고자료

[[토크ON세미나] JPA 프로그래밍 기본기 다지기 3강 - 필드와 컬럼 매핑 | T아카데미](https://www.youtube.com/watch?v=TYO42hpPLq4&t=1s)
