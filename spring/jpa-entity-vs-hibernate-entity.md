# javax.persistence.Entity Vs org.hibernate.annotations.Entity

## 서론

- JPA를 사용해 Entity를 매핑하려고 하면 해당 어노테이션이 두개가 나오는걸 확인할 수 있습니다.
- javax.persistence와 org.hibernate에 있는 어노테이션 두개입니다.
- 현재는 hibernate에 있는 애는 deprecated 되었기 떄문에 굳이 볼 필요는 없다고 생각하지만, 단순한 궁금증이 생겨 두개의 차이를 비교해보려고 합니다.
- 그러나 해당 속성들은 모두 다 다른 어노테이션으로 대처되어 사용되기 때문에 해당 속성들에 대한 내용들은 알아둬야 할것 같습니다.
- 해당 속성들에 대한 정보들이 많이 나오지 않아서 정확하지 않을 수도 있습니다. 틀린 부분이 있다면 가감히 지적해주세요.

## javax.persistence.Entity

```java
package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import java.lang.annotation.Documented;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * Specifies that the class is an entity. This annotation is applied to the
 * entity class.
 * 
 * @since Java Persistence 1.0
 */
@Documented
@Target(TYPE)
@Retention(RUNTIME)
public @interface Entity {

	/**
	 * (Optional) The entity name. Defaults to the unqualified
	 * name of the entity class. This name is used to refer to the
	 * entity in queries. The name must not be a reserved literal
	 * in the Java Persistence query language.
	 */
	String name() default "";
}
```

- Entity를 매핑할 때 사용하는 어노테이션입니다.
- name()이라는 속성만 있는 것을 확인할 수 있는데 JPQL에서 사용하는 이름을 지정하는 것이며, 기본적으로 Class Name을 따라가게 되는걸 확인할 수 있습니다.
- 사실 실제 봤을때도 Entity를 매핑하는 기능만 있는걸 확인할 수 있습니다.

## org.hibernate.annotations.Entity

```java
package org.hibernate.annotations;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * Extends {@link javax.persistence.Entity} with Hibernate features.
 *
 * @author Emmanuel Bernard
 *
 * @deprecated See individual attributes for intended replacements.  To be removed in 4.1
 */
@Target(TYPE)
@Retention(RUNTIME)
@Deprecated
public @interface Entity {
	/**
	 * Is this entity mutable (read only) or not.
	 *
	 * @deprecated use {@link org.hibernate.annotations.Immutable} 
	 */
	@Deprecated
	boolean mutable() default true;
	/**
	 * Needed column only in SQL on insert.
	 * @deprecated use {@link DynamicInsert} instead
	 */
	@Deprecated
	boolean dynamicInsert() default false;
	/**
	 * Needed column only in SQL on update.
	 * @deprecated Use {@link DynamicUpdate} instead
	 */
	@Deprecated
	boolean dynamicUpdate() default false;
	/**
	 *  Do a select to retrieve the entity before any potential update.
	 *  @deprecated Use {@link SelectBeforeUpdate} instead
	 */
	@Deprecated
	boolean selectBeforeUpdate() default false;
	/**
	 * polymorphism strategy for this entity.
	 * @deprecated use {@link Polymorphism} instead
	 */
	@Deprecated
	PolymorphismType polymorphism() default PolymorphismType.IMPLICIT;
	/**
	 * optimistic locking strategy.
	 * @deprecated use {@link OptimisticLocking} instead.
	 */
	@Deprecated
	OptimisticLockType optimisticLock() default OptimisticLockType.VERSION;
	/**
	 * persister of this entity, default is hibernate internal one.
	 * @deprecated  use {@link Persister} instead
	 */
	@Deprecated
	String persister() default "";
}
```

- JPA의 Entity의 확장버전으로 더 많은 속성을 지원해줍니다.
- 그러다 실제 Deprecated되었지만, 해당 속성들에 대해서는 각각 다른 어노테이션을 통해 지원을 해주고 있습니다. 각각의 속성을 살펴보도록 하겠습니다.

### mutable()

- org.hibernate.annotations.Immutable 대체
- 불변성을 보장할 것인지를 나타내는 것으로 true인 경우에는 불변이라는 의미이며 false인 경우에는 가변이라는 의미입니다.
- Entity의 값을 변경하려고 할때(update query) 변경이 불가하다는 것이다.
- 가장 큰 특징은 update를 실행시켰을 떄 예외를 던지는게 아니라 무시한다.
- 그러나 JPQL, Criteria로는 업데이트가 가능합니다. 5.2.17부터는 WARINNG으로 변경되었지만 그래도 가능합니다.
- 설정을 변경하면 예외를 던질 수 있다고 합니다.

[@Immutable in Hibernate | Baeldung](https://www.baeldung.com/hibernate-immutable)

### dynamicInsert()

- @org.hibernate.annotations.DynamicInsert 대체
- 데이터를 저장할 때 데이터가 존재하는 필드만으로 InsertSQL을 동적으로 생성한다.
- 즉, nullr값에 대해서는 생성하지 않는다는 의미입니다.

### dynamicUpdate()

- @org.hibernate.annotations.DynamicUpdate 대체
- 수정된 데이터만 동적으로 Update SQL을 생성한다.

### selectBeforeUpdate()

- @org.hibernate.annotations.SelectBeforeUpdate 대체
- 다시 영속화하였을때 엔티티가 업데이터가 되었는지 아닌지를 확인하고 검색을 진행하는 기능(?)

### polymorphism()

- @org.hibernate.annotations.Polymorphism 대체
- 다형성을 적용
- 종류
    - EMPLICIT
        - 명시적으로 요청된 경우에만 SELECT
    - IMPLICIT
        - 상위 항목이 SELECT되면 같이 SELECT

### optimisticLock()

- @org.hibernate.annotations.OptimisticLocking 대체
- 낙관적 잠금을 설정할 때 사용합니다.

### persister()

- @org.hibernate.annotations.Persister 대체
- 커스텀 영속성을 설정할 수 있습니다.

### 참고자료
[권남](https://kwonnam.pe.kr/wiki/java/hibernate/annotations)
