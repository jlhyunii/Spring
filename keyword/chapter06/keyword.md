## 1. 지연로딩과 즉시로딩의 차이

### 지연 로딩 (FetchType.LAZY)

DB가 아닌 **프록시에서 데이터를 가져온다.**<br>
예를 들자면, MemberPrefer을 조회할 때 Member, FoodCategory는 각각 분리되어 MemberPrefer은 실제 DB를, Member와 FoodCategory는 프록시를 조회하는 것이다.<br>

> 즉, MemberPrefer을 조회할 때, Member와 FoodCategory를 굳이 같이 조회할 필요가 없다면 분리하여 조회하는 지연 로딩 방법을 권장한다!

#### 그럼 여기서 말하는 프록시는 무엇인지 간단히 짚고 넘어가자.

프록시 객체는 가짜 객체로, 이를 호출하면 실제 객체의 메소드를 호출하게 된다.<br>
실제 클래스의 인터페이스를 구현하거나 클래스를 상속받아 실제 객체처럼 동작한다.

### 즉시 로딩 (FetchType.EAGER)

지연 로딩과는 달리, MemberPrefer과 Member, FoodCategory 모두를 실제 DB에서 즉시 한 번에 조회하여 하나의 쿼리로 가져온다.<br>

> 즉, MemberPrefer을 조회할 때 Member와 FoodCategory도 조회해야 한다면, FetchType을 EAGER로 설정해야한다.

<br>

#### 단점

1. 객체를 조회할 때, 관련된 모든 데이터를 한 번에 가져오기에 불필요한 데이터가 로딩될 수 있다.
2. 즉시 로딩된 데이터가 모두 메모리에 로드되므로 메모리 사용량이 많아진다.
3. **JPQL에서 N+1 문제가 발생할 수 있다.**

<br>
<br>

## 2. Fetch Join

JPQL에서 성능 최적화를 위해 제공하는 join의 종류로, N+1 문제의 해결책이다.

### Fetch Join + Lazy Loading

Root entity에 대해서 조회할 때 Lazy Loading으로 설정되어 있는 연관 관계를 join 쿼리를 발생시켜 한 번에 조회할 수 있는 기능이다.<br>

#### Fetch Join vs 일반 Join

Fetch Join은 ORM에서의 사용을 전제로 DB Schema를 entity로 자동 변환 해주고 영속성 컨텍스트에 영속화해준다.<br>

- **Fetch Join으로 조회** : 연관 관계는 영속성 컨텍스트 1차캐시에 저장되어 다시 entity 그래프를 탐색하더라도 조회 쿼리가 수행되지 않는다.
- **일반 Join으로 조회** : 단순히 데이터를 조회하는 개념으로 영속성 컨텍스트나 entity와는 무관하다.

따라서, Fetch Join을 활용하면 ORM 활용이 가능하여 RDB와의 패러다임 차이를 줄일 수 있다.

#### Collection 연관 관계 Fetch Join 시 주의사항

Collection에 대해서 Fetch Join을 할 경우, 1:N 관계이기에 1쪽의 데이터는 중복된 상태로 조회된다.<br>
이를 방지하려면? distinct 절을 활용해야한다. >> 중복되는 entity 제거 가능.

<br>
<br>

## 3. @EntityGraph

주로 N+1 문제를 해결하거나 즉시 로딩을 원하는 경우에 사용되는 어노테이션이다.<br>
이를 사용해 **특정 쿼리에서 지연 로딩 대신 명시적으로 관련 엔티티를 즉시 로딩**할 수 있다.

예를 들어 User 엔티티와 Address 엔티티가 연관된 경우, @EntityGraph를 사용하여 User를 조회하면서 Address도 함께 가져오는 것이 가능하다.

### @EntityGraph로 N+1 문제를 해결할 수 있는 이유

@EntityGraph로 연관 엔티티를 즉시 로딩하도록 하면, 쿼리 한 번에 필요한 데이터를 모두 가져와 추가 쿼리 발생을 방지할 수 있기 때문이다.

아래 코드를 통해 이해해보자.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    @EntityGraph(attributePaths = "address")
    List<User> findAll();
}
```

- UserRepository의 findAll 메소드는 User 엔티티를 조회할 때, 연관된 address 리스트를 즉시 로딩한다.
- 이때 address가 지연 로딩 설정이더라도 즉시 로딩되어 추가 쿼리는 발생하지 않는다.

#### 속성

- attributePaths : 즉시 로딩할 연관 엔티티의 속성 이름을 지정한다.

<br>
<br>

## 4. JPQL

JPA의 일부로, 쿼리를 데이터베이스의 테이블이 아닌 JPA 엔티티, **즉 '객체'를 대상으로 작성하는 객체 지향 쿼리 언어**이다.<br>
즉, SQL처럼 데이터베이스와 상호작용을 한다는 기본 컨셉은 가져가면서, 객체 지향적인 특성을 반영한 쿼리를 날릴 수 있도록 도와주는 것이다.

#### 그럼 JPQL과 SQL은 뭐가 다를까?

JPQL은 엔티티 객체를 대상으로 쿼리문을 작성하지만, SQL은 데이터베이스 테이블을 대상으로 쿼리문을 작성한다.

### JPQL을 사용하는 방법1 : repository 인터페이스

1. 메소드 이름으로 쿼리 생성
- Spring Data JPA는 메소드 이름을 기반으로 자동으로 쿼리를 생성해준다.
- 만약 우리가 name 필드와 status 필드를 조건으로 내세운 findByNameAndStatus 라는 메소드 이름을 정의하면 이에 맞게 JPQL 쿼리를 생성한다.
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
    List<Member> findByNameAndStatus(String name, MemberStatus status);
}

// SELECT * FROM member WHERE name = '베뉴' AND status = 'ACTIVE'; 과 동일
List<Member> member = memberRepository.findByNameAndStatus("베뉴", MemberStatus.ACTIVE);
```

2. @Query 어노테이션

- @Query 어노테이션을 활용하여 이름과 상태를 조건으로 조회하는 JPQL 쿼리를 작성한다.
- @Param 어노테이션을 통해 JPQL 쿼리에서 사용되는 :name 과 :status 파라미터를 메소드의 인자와 연결한다.
```java
public interface MemberRepository extends JpaRepository<Member, Long> {

    @Query("SELECT m FROM Member m WHERE m.name = :name AND m.status = :status")
    List<Member> findByNameAndStatus(@Param("name") String name, @Param("status") MemberStatus status);
}
```

### JPQL의 문제점

1. JPQL은 문자열 형태이기에 개발자에 의존적인 형태이다.
2. 컴파일 단계에서 Type=Check가 불가능하다.
3. 런타임 단계에서 오류 발견이 가능하다.

> 그래서 JPQL 보완을 위해 나온 것이 바로 **QueryDSL**이다.

<br>
<br>

## 5. QueryDSL

타입 안정성을 보장하는 자바 기반의 쿼리 빌더 라이브러리이다.<br>
이는 코드 기반의 쿼리 빌더를 제공하므로,
- 컴파일 시점에 쿼리의 오류를 잡을 수 있으며
- 동적 쿼리 작성이 편리하고
- 메서드 체이닝을 통한 복잡한 쿼리 작성에 유리하다.

그렇다면 여기서 **동적 쿼리**란 뭘까?

### 동적 쿼리

실행 시점에 쿼리의 일부가 변경될 수 있는 쿼리이다.<br>
요구사항에 따라 쿼리의 조건 혹은 구조가 유동적으로 조정되어야 하는 경우가 있는데, 이때 동적 쿼리의 사용이 중요하다.

#### 작성 방법

1. BooleanBuilder
2. Where 다중 파라미터

- BooleanBuilder는 QueryDSL에서 여러 개의 조건을 조합하기 위해 제공되는 빌더 클래스이다.
- 즉, 여러 조건을 동적으로 추가할 수 있다.
- .and() 나 .or() 등의 간단한 논리 연산으로 Where문에 조건을 연결할 수 있다.

### JPQL vs QueryDSL

아래 코드로 이해해보자.

```java
//JPQL
String username = "java";
String jpql = "select m from Member m where m.username = :username";

List<Member> result = em.createQuery(query, Member.class).getResultList();


// QueryDSL
String username = "java";

List<Member> result = queryFactory
        .select(member)
        .from(member)
        .where(usernameEq(username))
        .fetch();
```

### QueryDSL 장점

- 문자가 아닌 코드로 쿼리 작성이 가능하여 컴파일 시점에 문법 오류를 확인할 수 있다.
- 복잡한 쿼리나 동적 쿼리 작성에 편리하다.
- 쿼리 작성 시 제약 조건 등을 메소드 추출을 통해 재사용할 수 있다.
- JPQL 문법과 유사한 형태로 작성이 가능하여 쉽게 적응할 수 있다.