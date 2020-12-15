# 엔티티 생명주기(Entity LifeCycle)

![jpa-entity-life-cycle-1](https://github.com/ksy90101/TIL/blob/master/jpa/imgge/jpa-entity-life-cycle-1.png)

```java
@DisplayName("엔티티 생명주기 테스트")
    @Test
    void entityLifeCycleTest() {
        final EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
        final EntityManager em = emf.createEntityManager();
        final EntityTransaction transaction = em.getTransaction();

        transaction.begin();

        try {
            System.out.println("===== 영속 상태 테스트====");
            final Team team = new Team(1L, "Team A"); // 준영속 상태
            em.persist(team); // 영속 상태
            final Member memberA = new Member(1L, null, "Member A"); // 준영속 상태
            em.persist(memberA); // 영속 상태

            em.flush();

            System.out.println("===== 준영속 상태 테스트 ====");
            em.detach(memberA); // 준영속

            final Member member = em.find(Member.class, memberA.getId());// 영속상태로 변경 (SELECT 쿼리가 나감)

            em.flush();
            System.out.println("==== 삭제 테스트 ====");

            em.remove(member); // 삭제 (삭제 쿼리)

            transaction.commit();
        } catch (final Exception e) {
            transaction.rollback();
        } finally {
            em.close();
        }

        emf.close();
    }
```

```sql
===== 영속 상태 테스트====
Hibernate: 
    insert 
    into
        Team
        (name, id) 
    values
        (?, ?)
Hibernate: 
    insert 
    into
        Member
        (name, team_id, id) 
    values
        (?, ?, ?)
===== 준영속 상태 테스트 ====
Hibernate: 
    select
        member0_.id as id1_0_0_,
        member0_.name as name2_0_0_,
        member0_.team_id as team_id3_0_0_ 
    from
        Member member0_ 
    where
        member0_.id=?
==== 삭제 테스트 ====
Hibernate: 
    delete 
    from
        Member 
    where
        id=?
```

### 비영속(new / transient)

- 영속성 컨텍스트와 전혀 관계가 없는 상태
- 객체를 생성만 한 상태

![jpa-entity-life-cycle-2](https://github.com/ksy90101/TIL/blob/master/jpa/imgge/jpa-entity-life-cycle-2.png)

```java
Member member = new Member(1L, team, "MEMBER A");
```

### 영속(managed)

![jpa-entity-life-cycle-3](https://github.com/ksy90101/TIL/blob/master/jpa/imgge/jpa-entity-life-cycle-3.png)

- 영속성 컨텍스트에 저장된 상태
- 즉, Entity가 영속성 컨텍스트에 의해 관리되는 상태
- 영속성 컨텍스트가 관리하고 있기 때문에 Dirty Checking, Update Query가 가능하다.
- 영속 상태라고 해서 DB에 저장되어 있지 않을 수도 있다.
- 그렇다면 언제 DB에 저장되는가? 바로 트랜잭션 commit 시점에 영속성 컨텍스트에 있는 정보들이 DB에 쿼리로 날라갑니다.

```java
Member member = new Member(1L, team, "MEMBER A"); // 비영속

entityManager.persist(member); // 영속
```

- entityManager.persist()
    - 영속성 컨텍스트에 저장된 상태
- entityManager.find()
    - 영속성 컨텍스트에 없어서 DB에 조회한 후에 영속성 컨텍스트에서 영속 상태를 만든것

### 준영속(detached)

- 영속성 컨텍스트에 저장되었다가 분리된 상태
- 영속성 컨텍스트에서 지운 상태
- 영속성 컨텍스트가 관리하고 있지 않기 때문에 Dirty Checking, Update Query가 불가하다.

### 준영속 성태로 만드는 방법 (영속 → 준영속)

- entityManager.detach(entity);
    - 특정 엔티티만 준영속 상태로 만듦
- entityManager.clear();
    - 영속성 컨텍스트를 초기화
- entityManager.close();
    - 영속성 컨텍스트를 종료

```java
Member member = new Member(1L, team, "MEMBER A"); // 비영속

entityManager.persist(member); // 영속

entityManager.detach(member); // 준영속
```

### 삭제(delete)

- 실제 DB에서 삭제한 상태

```java
Member member = new Member(1L, team, "MEMBER A"); // 비영속

entityManager.persist(member); // 영속

entityManager.remove(member); // 삭제
```

## 참고자료

[[JPA] 엔티티의 생명주기 (Entity LifeCycle) - Heee's Development Blog](https://gmlwjd9405.github.io/2019/08/08/jpa-entity-lifecycle.html)

[[토크ON세미나] JPA 프로그래밍 기본기 다지기 6강 - JPA 내부구조 | T아카데미](https://www.youtube.com/watch?v=PMNSeD25Qko)
