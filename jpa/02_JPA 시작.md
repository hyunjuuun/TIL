h2 데이터베이스

h2.bat 파일 실행

JPA 구현체로 하이버네이트를 사용하기 위한 핵심 라이브러리

- hibernate-core: 하이버네이트 라이브러리
- hibernate-entitymanager: 하이버네이트가 JPA 구현체로 동작하도록 JPA 표준을 구현한 라이브러리
- hibernate-jpa-2.1-api: JPA2.1 표준 API를 모아둔 라이브러리

## 2.4 객체 매핑

---

@Entity

: 이 클래스를 테이블과 매핑한다고 JPA에게 알려준다. 이렇게 @Entity가 사용된 클래스를 엔티티 클래스라 한다.

@Table

: 엔티티 클래스에 매핑할 테이블 정보를 알려준다. 이 어노테이션을 생략하면 클래스 이름을 테이블 이름으로 매핑한다. (더 정확히는 엔티티 이름을 사용한다. 엔티티 이름은 4.1절에서 설명한다.)

@Id

: 엔티티 클래스의 필드를 테이블의 기본 키(Primary key)에 매핑한다. 이렇게 @Id가 사용된 필드를 식별자 필드라 한다.

@Column

: 필드를 컬럼에 매핑한다.

매핑 정보가 없는 필드

: 매핑 어노테이션을 생략하면 필드명을 사용해서 컬럼명으로 매핑한다.

만약 대소문자를 구분하는 데이터베이스를 사용하면 @Column(name="AGE")처럼 명시적으로 매핑해야 한다.

## 2.6 애플리케이션 개발

---

### 1. 엔티티 매니저 설정

엔티티 매니저의 생성 과정

1. META-INF/persistence.xml 파일을 통해 설정 정보 조회 (Persistence)
2. EntityManagerFactory 생성
3. EntityManager 생성

**엔티티 매니저 팩토리 생성**

JPA를 시작하려면 우선 persistenc.xml의 설정 정보를 사용해서 엔티티 매니저 팩토리를 생성해야 한다. 이 때 Persistence 클래스를 사용하는데 이 클래스는 엔티티 매니저 팩토리를 생성해서 JPA를 사용할 수 있게 준비한다.

```java
EntityManagerFactory emf = 
	Persistence.createEntityManagerFactory("jpabook");
```

이름이 jpabook인 영속성 유닛(persistence-unit)을 찾아서 엔티티 매니저 팩토리를 생성한다.

이때 persistence.xml의 설정 정보를 읽어서 JPA를 동작시키기 위한 기반 객체를 만들고 JPA 구현체에 따라서는 데이터베이스 커넥션 풀도 생성하므로 엔티티 매니저 팩토리를 생성하는 비용은 아주 크다. 따라서 **엔티티 매니저 팩토리는 애플리케이션 전체에서 딱 한 번만 생성하고 공유해서 사용해야 한다.**

**엔티티 매니저 생성**

```java
EntityManger em = emf.createEntityManager();
```

JPA의 기능 대부분은 엔티티 매니저가 제공한다. (등록/수정/삭제/조회)

엔티티 매니저는 내부에 데이터소스(데이터베이스 커넥션)을 유지하면서 데이터베이스와 통신한다. 따라서 애플리케이션 개발자는 엔티티 매니저를 가상의 데이터베이스로 생각할 수 있다.

**참고로 엔티티 매니저는 데이터베이스 커넥션과 밀접한 관계가 있으므로 스레드 간에 공유하거나 재사용하면 안 된다.**

**종료**

마지막으로 사용이 끝난 엔티티 매니저는 반드시 종료해야 한다.

```java
em.close(); // 엔티티 매니저 종료
```

애플리케이션을 종료할 때 엔티티 매니저 팩토리도 다음처럼 종료해야 한다.

```java
emf.close(); // 엔티티 매니저 팩토리 종료
```

### 2. 트랜잭션 관리

JPA를 사용하면 항상 트랜잭션 안에서 데이터를 변경해야 한다. 트랜잭션 없이 데이터를 변경하면 예외가 발생한다.

```java
EntityTransaction tx = em.getTransaction();

try {
		tx.begin(); // 트랜잭션 - 시작
		logic(em); // 비즈니스 로직 실행
		tx.commit(); // 트랜잭션 - 커밋
} catch (Exception e) {
		tx.rollback(); // 트랜잭션 롤백
}
```

트랜잭션 API를 사용해서 비즈니스 로직이 정상 동작하면 트랜잭션을 커밋(commit)하고 예외가 발생하면 트랜잭션을 롤백(rollback)한다.

### 3. 비즈니스 로직

비즈니스 로직을 보면 등록, 수정, 삭제, 조회 작업이 엔티티 매니저를 통해서 수행되는 것을 알 수 있다. 엔티티 매니저는 객체를 저장하는 가상의 데이터베이스처럼 보인다.

```java
String id = "id1";
Member member = new Member();
member.setId(id);
member.setUsername("지한");
member.setAge(2);

// 등록
em.persist(member);
```

```java
// 수정
member.setAge(20);
```

JPA는 어떤 엔티티가 변경되었는지 추적하는 기능을 갖추고 있다. 따라서 member.setAge(20)처럼 엔티티의 값만 변경하면 UPDATE 쿼리를 날려준다. (사실 em.update()라는 메서드도 없다.)

```java
// 삭제
em.remove(member);
```

```java
// 한 건 조회
Member findMember = em.find(Member.class, id);
```

find() 메서드는 조회할 엔티티 타입과 @Id로 데이터베이스 테이블의 기본 키와 매핑한 식별자 값으로 엔티티 하나를 조회하는 가장 단순한 조회 메서드다.

### 4. JPQL

```java
// 목록 조회
TypedQuery<Member> query =
		em.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();
```

JPA를 사용하면 애플리케이션 개발자는 엔티티 객체를 중심으로 개발하고 데이터베이스에 대한 처리는 JPA에 맡겨야 한다.

문제는 검색 쿼리다. JPA는 엔티티 객체를 중심으로 개발하므로 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색해야 한다.

그런데 테이블이 아닌 엔티티 객체를 대상으로 검색하려면 데이터베이스의 모든 데이터를 애플리케이션으로 불러와서 엔티티 객체로 변경한 다음 검색해야 하는데, 이는 사실상 불가능하다. 애플리케이션이 필요한 데이터만 데이터베이스에서 불러오려면 결국 검색 조건이 포함된 SQL을 사용해야 한다.

JPA는 JPQL(Java Persistence Query Language)이라는 쿼리 언어로 이 문제를 해결한다.

- JPQL은 **엔티티 객체**를 대상으로 쿼리한다. 쉽게 이야기해서 클래스와 필드를 대상으로 쿼리한다.
- SQL은 **데이터베이스 테이블**을 대상으로 쿼리한다.

상단의 목록 조회 예제에서 select m from Member m이 바로 JPQL이다. 여기서 from Member는 회원 엔티티 객체를 말하는 것이지 Member 테이블이 아니다. **JPQL은 데이터베이스 테이블을 전혀 알지 못한다.**

JPQL을 사용하려면 먼저 em.createQuery(JPQL, 반환 타입) 메서드를 실행해서 쿼리 객체를 생성한 후 쿼리 객체의 getLesultList() 메서드를 호출하면 된다.

JPA는 JPQL을 분석하여 적절한 SQL을 만들어 데이터베이스에서 데이터를 조회한다.

> SELECT M.ID, M.NAME, M.AGE FROM MEMBER M
>
