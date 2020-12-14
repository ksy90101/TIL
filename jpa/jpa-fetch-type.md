# JPA 페치(Fetch) 전략 - 즉시 로딩(EAGER)과 지연 로딩(LAZY)

## 예제코드 세팅

```java
import javax.persistence.Entity;
import javax.persistence.FetchType;
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

```java
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Team {
    @Id
    private Long id;

    private String name;

    public Team() {
    }

    public Team(final Long id, final String name) {
        this.id = id;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

```java
import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

public class FetchTest {
    @DisplayName("Fetch 테스트")
    @Test
    void SaveAndFindForObj() {
        final EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
        final EntityManager em = emf.createEntityManager();
        final EntityTransaction transaction = em.getTransaction();

        transaction.begin();

        try {
            final Team team = new Team(1L, "Team A");
            em.persist(team);
            final Member member = new Member(1L, team, "Member A");
            em.persist(member);

            em.flush();
            em.clear();

            final Member findMember = em.find(Member.class, member.getId());
						final Member findMember = em.find(Member.class, member.getId());
            final Team findTeam = findMember.getTeam();
            System.out.println(findTeam);
            
						assertAll(
                    () -> assertThat(findMember.getId()).isEqualTo(member.getId()),
                    () -> assertThat(findMember.getTeam().getId()).isEqualTo(member.getTeam().getId()),
                    () -> assertThat(findMember.getName()).isEqualTo(member.getName())
            );

            transaction.commit();
        } catch (final Exception e) {
            transaction.rollback();
        } finally {
            em.close();
        }

        emf.close();
    }
}
```

## 즉시 로딩(EAGER)

- 엔티티를 조회할 떄 연관된 엔티티도 함께 조회한다.
- 즉시로딩을 사용하면 실제 team의 엔티티를 불러온다.
- 즉시 로딩을 최적화하기 위해 가능하면 조인쿼리를 사용한다.
- `@ManyToOne(fetch = FetchType.EAGER)`
- 위와 같이 Member에 넣으면 아래와 같은 SQL이 실행되는걸 확인할 수 있습니다.

```sql
Hibernate: 
    select
        member0_.id as id1_0_0_,
        member0_.name as name2_0_0_,
        member0_.team_id as team_id3_0_0_,
        team1_.id as id1_1_1_,
        team1_.name as name2_1_1_ 
    from
        Member member0_ 
    left outer join
        Team team1_ 
            on member0_.team_id=team1_.id 
    where
        member0_.id=?
```

### JPA 조인 전략

- 내부 조인(INNER JOIN)이 아닌 외부 조인(LEFT OUTER JOIN)을 사용하는 것을 볼 수 있다.
- 회원 테이블에서 TEAM이 null을 허용하고 있기 때문에 팀이 속하지 않은 회원까지 조회해야 하기 때문에 외부 조인을 사용한다.
- 그러나 외부 조인보다 내부 조인이 성능과 최적화에서 더 뛰어나다. 그렇다면 내부 조인을 사용하려면 어떻게 해야 하는가?
- 바로 NOT NULL 제약 조건을 설정하면 된다.
- 아래와 같이 값을 준다면, 그 아래와 같이 내부 조인 SQL을 확인할 수 있다.

```
@ManyToOne(fetch = FetchType.EAGER)
@JoinColumn(name = "team_id", nullable = false)
```

```sql
select
        member0_.id as id1_0_0_,
        member0_.name as name2_0_0_,
        member0_.team_id as team_id3_0_0_,
        team1_.id as id1_1_1_,
        team1_.name as name2_1_1_ 
    from
        Member member0_ 
    inner join
        Team team1_ 
            on member0_.team_id=team1_.id 
    where
        member0_.id=?
```

## 지연 로딩(LAZY)

- 연관된 엔티티를 실제 사용할 때 조회한다.
- 이떄 실제 TEAM 객체가 아닌 프록시 TEAM 객체를 넣어놓는다.
- `@ManyToOne(fetch = FetchType.LAZY)`
- 위와 같이 Member에 넣으면 아래와 같은 SQL이 실행되는걸 확인할 수 있습니다.

```sql
Hibernate: 
    select
        member0_.id as id1_0_0_,
        member0_.name as name2_0_0_,
        member0_.team_id as team_id3_0_0_ 
    from
        Member member0_ 
    where
        member0_.id=?
Hibernate: 
    select
        team0_.id as id1_1_0_,
        team0_.name as name2_1_0_ 
    from
        Team team0_ 
    where
        team0_.id=?
```

## JPA 기본 Fetch 전략

### 즉시 로딩(FetchType.EAGER)

- @ManyToOne
- @OneToOne

### 지연 로딩(FetchType.LAZY)

- @OneToMany
- @ManyToMany

## 결론

- 컬렉션에서는 무조건 지연로딩을 사용하고 단일 엔티티는 즉시 로딩을 사용한다.
- 그러나 추천하는 방법은 모든 연관관계에 지연 로딩을 사용하고 필요한 곳에만 즉시 로딩을 사용하는 것이다.
- 성능적으로 좀 더 좋게 사용할 수 있기 때문이다.

## 참고자료

- [자바 ORM 표준 JPA 프로그래밍 - 김영한](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=62681446)
