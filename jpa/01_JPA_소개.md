# 1. JPA 소개
저자가 들었던 의문

- "왜 실무에서 테이블 설계는 다들 열심히 하면서 제대로 된 객체 모델링은 하지 않을까?"
- "왜 객체지향의 장점을 포기하고 객체를 단순히 테이블에 맞추어 데이터 전달 역할만 하도록 개발할까?"

의문을 스스로 해결해보기 위해 객체 모델링을 적용해보았지만, 객체 모델링을 세밀하게 진행할수록 객체를 데이터베이스에 저장하거나 조회하기는 점점 더 어려워졌고, 객체와 관계형 데이터베이스의 차이를 메우기 위해 더 많은 SQL을 작성해야 했다.

JPA는 지루하고 반복적인 CRUD SQL을 알아서 처리해줄 뿐만 아니라 객체 모델링과 관계형 데이터베이스 사이의 차이점도 해결해주었다.

JPA를 사용해서 얻은 가장 큰 성과는 애플리케이션을 SQL이 아닌 객체 중심으로 개발하니 생산성과 유지보수가 확연히 좋아졌고 테스트를 작성하기도 편리해진 점이다.

지금부터 SQL을 직접 다룰 때 어떤 문제가 발생하는지, 객체와 관계형 데이터베이스 사이에는 어떤 차이가 있는지 자세히 알아보자.

## 1.1 SQL을 직접 다룰 때 발생하는 문제점

---

### 1. 반복

회원 객체를 데이터베이스가 아닌 자바 컬렉션에 보관한다면 어떨까? 컬렉션은 다음 한 줄로 객체를 저장할 수 있다.

```java
list.add(member);
```

하지만 데이터베이스는 객체 구조와는 다른 데이터 중심의 구조를 가지므로 객체를 데이터베이스에 직접 저장하거나 조회할 수는 없다. 따라서 개발자가 객체지향 애플리케이션과 데이터베이스 중간에서 SQL과 JDBC API를 사용하여 변환 작업을 직접 해주어야 한다.

### 2. SQL에 의존적인 개발

Member나 Team처럼 비즈니스 요구사항을 모델링한 객체를 엔티티라 하는데, SQL에 모든 것을 의존하는 상황에서는 개발자들이 엔티티를 신뢰하고 사용할 수 없다. 대신에 DAO를 열어서 어떤 SQL이 실행되고 어떤 객체들이 함께 조회되는지 일일이 확인해야 한다.

애플리케이션에서 SQL에서 직접 다룰 때 발생하는 문제점

- 진정한 의미의 계층 분할이 어렵다.
- 엔티티를 신뢰할 수 없다.
- SQL에 의존적인 개발을 피하기 어렵다.

### 3. JPA와 문제 해결

JPA가 제공하는 CRUD API를 간단히 알아보자.

```java
jpa.persist(member); // 저장

String memberId = "helloId";
Member member = jpa.find(Member.class, memberId); // 조회

Member member = jpa.find(Member.class, memberId);
member.setName("이름변경"); // 수정

Member member = jpa.find(Member.class, memberId);
Team team = member.getName(); // 연관된 객체 조회
```

- JPA는 별도의 수정 메서드를 제공하지 않는다. 대신에 객체를 조회해서 값을 변경만 하면 트랜잭션을 커밋할 때 데이터베이스에 적절한 UPDATE SQL이 전달된다.

## 1.2 패러다임의 불일치

---

비즈니스 요구사항을 정의한 도메인 모델도 객체로 모델링하면 객체지향 언어가 가진 장점들을 활용할 수 있다. 문제는 이렇게 정의한 도메인 모델을 저장할 때 발생한다.

예를 들어 특정 유저가 시스템에 회원 가입하면 회원이라는 객체 인스턴스를 생성한 후에 이 객체를 메모리가 아닌 어딘가에 영구 보관해야 한다.

객체는 속성(필드)과 기능(메서드)을 가진다. 객체의 기능은 클래스에 정의되어 있으므로 객체 인스턴스의 상태인 속성만 저장했다가 필요할 때 불러와서 복구하면 된다. 하지만 객체가 부모 객체를 상속 받았거나, 다른 객체를 참조하고 있다면 객체의 상태를 저장하기는 쉽지 않다. (연관된 객체들을 함께 저장해야하는 문제)

관계형 데이터베이스는 데이터 중심으로 구조화되어 있고, 집합적인 사고를 요구한다. 그리고 객체지향에서 이야기하는 추상화, 상속, 다형성 같은 개념이 없다.

객체와 관계형 데이터베이스는 지향하는 목적이 서로 다르므로 둘의 기능과 표현 방법도 다르다. 이것을 객체와 관계형 데이터베이스의 패러다임 불일치 문제라 한다. 따라서 객체 구조를 테이블 구조에 저장하는 데는 한계가 있다.

패러다임의 불일치로 발생하는 문제를 구체적으로 살펴보자.

### 1. 상속

```java
abstract class Item {
		Long id;
		String name;
		int price;
}

class Album extends Item {
		String artist;
}

class Movie extends Item {
		String director;
		String actor;
}

class Book extends Item {
		String author;
		String isbn;
}
```

예를 들어 Album 객체를 저장하려면 이 객체를 분해해서 다음 두 SQL을 만들어야 한다.

INSERT INTO ITEM ...

INSERT INTO ALBUM ...

Album을 조회한다면 ITEM과 ALBUM 테이블을 조인해서 조회한 다음 그 결과로 Album 객체를 생성해야 한다.

만약 해당 객체들을 데이터베이스가 아닌 자바 컬렉션에 보관한다면 다음 같이 부모 자식이나 타입에 대한 고민 없이 해당 컬렉션을 그냥 사용하면 된다.

list.add(album);

list.add(movie);

Album album = list.get(albumId);

**JPA와 상속**

JPA는 상속과 관련된 패러다임의 불일치 문제를 개발자 대신 해결해준다. 개발자는 마치 자바 컬렉션에 객체를 저장하듯이 JPA에게 객체를 저장하면 된다.

### 2. 연관관계

JPA는 연관관계와 관련된 패러다임의 불일치 문제를 해결해준다.

```java
member.setTeam(team); // 회원과 팀 연관관계 설정
jpa.persist(member); // 회원과 연관관계 함께 저장
```

### 3. 객체 그래프 탐색

객체에서 회원이 소속된 팀을 조회할 때는 다음처럼 참조를 사용하여 연관된 팀을 찾으면 되는데, 이것을 객체 그래프 탐색이라 한다.

```java
Team team = member.getTeam();

member.getOrder().getOrderItem()... // 자유로운 객체 그래프 탐색
```

SQL을 직접 다루면 처음 실행하는 SQL에 따라 객체 그래프를 어디까지 탐색할 수 있는지 정해진다.

**JPA와 객체 그래프 탐색**

JPA는 연관된 객체를 사용하는 시점에 적절한 SELECT SQL을 실행한다. 따라서 JPA를 사용하면 연관된 객체를 신뢰하고 마음껏 조회할 수 있다. 이 기능은 실제 객체를 사용하는 시점까지 데이터베이스 조회를 미룬다고 해서 **지연 로딩**이라 한다.

```java
// 처음 조회 시점에 SELECT MEMBER SQL
Member member = jpa.find(Member.class, memberId);

Order order = member.getOrder();
order.getOrderDate(); // Order를 사용하는 시점에 SELECT ORDER SQL
```

Member를 사용할 때마다 Order를 함께 사용하면, 이렇게 한 테이블씩 조회하는 것보다는 Member를 조회하는 시점에 SQL 조인을 사용해서 Member와 Order를 함께 조회하는 것이 효과적이다.

JPA는 연관된 객체를 즉시 함께 조회할지 아니면 실제 사용되는 시점에 지연해서 조회할지를 간단한 설정으로 정의할 수 있다. 만약 Member와 Order를 즉시 함께 조회하겠다고 설정하면 JPA는 Member를 조회할 때 다음 SQL을 실행해서 연관된 Order도 함께 조회한다.

SELECT M.*, O.*

FROM MEMBER M

JOIN ORDER O ON M.MEMBER_ID = O.MEMBER_ID

### 4. 비교

데이터베이스는 기본 키의 값으로 각 로우(row)를 구분한다. 반면에 객체는 동일성(identity) 비교와 동등성(equality) 비교라는 두 가지 비교 방법이 있다.

- 동일성 비교는 == 비교다. 객체 인스턴스의 주소 값을 비교한다.
- 동등성 비교는 equals() 메서드를 사용해서 객체 내부의 값을 비교한다.

따라서 테이블의 로우를 구분하는 방법과 객체를 구분하는 방법에는 차이가 있다.

JPA는 같은 트랜잭션일 때 같은 객체가 조회되는 것을 보장한다.

```java
String memberId = "100";
Member member1 = jpa.find(Member.class, memberId);
Member member2 = jpa.find(Member.class, memberId);

member1 == member2; // 같다.
```

객체 비교하기는 분산 환경이나 트랜잭션이 다른 상황까지 고려하면 더 복잡해진다. (자세한 내용은 추후에 다룬다.)

### 5. 정리

JPA는 객체 모델과 관계형 데이터베이스 모델의 패러다임 불일치 문제를 해결해주고 정교한 객체 모델링을 유지하게 도와준다.

## 1.3 JPA란 무엇인가?

---

Java Persistence API

자바 진영의 ORM 기술 표준

ORM이란? (Object-Relational Mapping)

이름 그대로 객체와 관계형 데이터베이스를 매핑한다는 뜻이다. ORM 프레임워크는 객체와 테이블을 매핑해서 패러다임의 불일치 문제를 개발자 대신 해결해준다.

### 1. JPA 소개

### 2. 왜 JPA를 사용해야 하는가?

**생산성, 유지보수, 패러다임의 불일치 해결, 성능**

SQL을 작성하고 JDBC API를 사용하는 지루하고 반복적인 일을 JPA가 대신 처리해준다.

```java
jpa.persist(member); // 저장
Member member = jpa.find(memberId); // 조회
```
