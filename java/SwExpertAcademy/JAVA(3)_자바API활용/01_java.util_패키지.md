# 01. java.util 개요 및 Utility 클래스

#TIL/java

---

## java.util 패키지

자바 프로그램을 개발할 때 유용한 기능들을 모아놓은 패키지

<br>

### Date 클래스

형식이 있는 날짜와 시간을 출력하는 클래스

<br>

![date 클래스](./images/01_01.png)

<br>

### Calendar 클래스

Date 클래스처럼 날짜와 시간에 관한 정보를 출력할 때 사용

추상 클래스이므로 직접 객체 생성은 불가능 / getInstance() 메서드를 이용하여 객체 생성 가능

<br>

### Formatter 클래스

<br>

```java
Formatter formatter = new Formatter(Appendable a);
```

<br>

Formatter 객체 생성을 위해서는 Appenable 인터페이스를 구현한 클래스를 사용해야 한다.

- Formatter 에서 형식화 된 문자열을 만들었을 때, 결과가 저장되는 곳
  - Appendable 인터페이스를 구현한 대표적인 클래스 - StringBuffer

<br>

```java
StringBuffer sb = new StringBuffer();
Formatter formatter = new Formatter(sb);
```

> Formatter 객체에서 적용한 출력 포맷 결과가 StringBuffer 객체에 저장된다.

<br>

## Collection

### 배열의 한계

배열은 고정 길이를 사용하므로 배열이 한 번 생성되면 길이가 증가하거나 감소할 수 없다. (따라서 배열을 사용할 때는 충분한 크기로 설정해야 한다.)

<br>

### Vector 클래스

자바에서는 동적인 길이로 다양한 객체들을 저장하기 위해 Vector 클래스를 제공한다. (일종의 가변 길이 배열)

<br>

### Stack 클래스

스택에 저장된 요소들 중 첫번째 요소에 대해서만 삽입이나 삭제가 가능한 자료구조

LIFO(Last In First Out) - 후입선출

top <-> bottom

<br>

### Queue 인터페이스

FIFO(First In First Out) - 선입선출

front <-> rear

<br>

## Generics

컬렉션에 저장할 객체의 타입을 제한할 수 있게 해준다.

- 타입의 안전성 제공
- 타입 체크와 형변환 과정 생략
- 코드의 간결화





