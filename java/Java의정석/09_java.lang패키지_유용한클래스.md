# 09. java.lang 패키지와 유용한 클래스

#TIL/java/자바의정석

---

java.lang 패키지는 자바 프로그래밍에 가장 기본이 되는 클래스들을 포함하고 있다.

- import문 없이 사용 가능 (String, System 클래스 등)

<br>

## Object 클래스

모든 클래스의 최고 조상

멤버 변수는 없고 11개의 메서드를 가지고 있다.

<br>

### equals(Object obj)

매개변수로 객체의 참조변수를 받아서 비교하여 그 결과를 boolean 값으로 알려준다.

```java
public boolean equals(Object obj){
  return (this==obj);
}
```

두 객체의 같고 다름을 참조변수의 값으로 판단한다. (주소값 비교)

<br>
String, Date, File, wrapper클래스(Integer,Double 등)의 equals메서드들은 주소값이 아닌 내용을 비교하도록 오버라이딩 되어 있다.

<br>

### hashCode()

해싱(hashing) 기법에 사용되는 '해시함수(hash function)'를 구현한 것이다. 해싱은 데이터관리기법 중 하나로 다량의 데이터를 저장하고 검색하는 데 유용하다.

해시함수는 찾고자하는 값을 입력하면, 그 값이 저장된 위치를 알려주는 해시코드(hash code)를 반환한다. (같은 객체라면 hashCode 메서드를 호출했을 때의 결과값인 해시코드가 같아야 한다.)

<br>

String 클래스는 문자열의 내용이 같으면 동일한 해시코드를 반환하도록 hashCode 메서드가 오버라이딩 되어있다. 

반면에 System.identityHashCode(Object x)는 Object 클래스의 hashCode 메서드처럼 객체의 주소값으로 해시코드를 생성하므로 모든 객체에 대해 항상 다른 해시코드값을 반환한다.

> System.identityHashCode(Object x)의 호출결과는 실행 할 때마다 달라질 수 있다.

<br>

### toString()

toString 메서드는 인스턴스에 대한 정보를 문자열로 제공한다.

```java
public String toString(){
  return getClass().getName()+"@"+Integer.toHexString(hashCode());
}
```

<br>

String 클래스의 toString()은 String 인스턴스가 가지고 있는 문자열을 반환하도록 오버라이딩 되어 있다.

Date 클래스의 toString()은 Date 인스턴스가 가지고 있는 날짜와 시간을 문자열로 변환하여 오버라이딩 되어 있다.

<br>

### clone()

clone 메서드는 자신을 복제하여 새로운 인스턴스를 생성한다.

Object 클래스에 정의된 clone()은 인스턴스변수의 값만 복사하므로 참조타입의 인스턴스 변수가 있는 클래스는 완전한 인스턴스 복제가 이루어지지 않는다.

clone()을 사용하려면, 먼저 복제할 클래스가 Cloneable 인터페이스를 구현해야하고, clone()을 오버라이딩하면서 접근 제어자를 protected에서 public으로 변경한다. 

<br>

### 공변 반환타입

JDK1.5부터 추가

오버라이딩 시 조상 메서드의 반환타입을 자손 클래스의 타입으로 변경을 허용하는 것이다.

```java
public Point clone(){ // 반환타입을 Object에서 Point로 변경
	Object obj = null;
  try {
    obj = super.clone();
  } catch(CloneNotSupportedException e){}
  return (Point)obj; // Point타입으로 형변환
}
```

조상의 타입이 아닌, 실제로 반환되는 자손 객체의 타입으로 반환이 가능하므로 번거로운 형변환이 줄어든다는 장점이 있다.

<br>

```java
int[] arr = {1,2,3,4,5};
int[] arrClone = arr.clone();
```

```java
int[] arr = {1,2,3,4,5};
int[] arrClone = new int[arr.length]; // 배열을 생성하고
System.arraycopy(arr,0,arrClone,0,arr.length); // 배열을 복사한다.
```

위 두 코드는 같은 결과를 얻는다.

<br>

배열 뿐 아니라 java.util 패키지의 Vector, ArrayList, LinkedList, HashSet, TreeSet, HashMap, TreeMap, Calendar, Date 등의 클래스들을 같은 방식으로 복제할 수 있다.

```java
ArrayList list = new ArrayList();
//...
ArrayList list2 = (ArrayList)list.clone();
```

<br>

### 얕은 복사와 깊은 복사

clone() 메서드는 단순 객체에 저장된 값을 그대로 복제한다. (객체가 참조하고 있는 객체까지 복제하지는 않는다.)

객체배열을 clone()으로 복제하는 경우에는 원본과 복제본이 같은 객체를 공유하므로 완전한 복제라고 보기 어렵다. 이러한 복제를 '얕은 복사(shallow copy)'라고 한다. 얕은 복사에서는 원본을 변경하면 복제본도 영향을 받는다.

반대로 원본과 복사본이 서로 다른 객체를 참조하도록 하는 복사를 '깊은 복사(deep copy)'라고 한다.

<br>

### getClass()

자신이 속한 클래스의 Class객체를 반환하는 메서드이다. Class 객체는 클래스의 모든 정보를 담고 있으며, 클래스 당 1개만 존재한다.

클래스 파일이 '클래스 로더(ClassLoader)'에 의해서 메모리에 올라갈 때, 자동으로 생성된다.

<br>

### Class 객체를 얻는 방법

클래스의 정보가 필요하면 Class 객체에 대한 참조를 얻어와야 한다. 참조를 얻는 방법은 여러가지이다.

```java
Class cObj = new Card().getClass(); // 생성된 객체로부터 얻는 방법
Class cObj = Card.class; // 클래스 리터럴(*.class)로부터 얻는 방법
Class cobj = Class.forName("Card"); // 클래스 이름으로부터 얻는 방법
```

Class 객체를 이용한 객체 생성

```java
Card c = new Card(); // new 연산자를 이용한 객체 생성
Card c = Card.class.newInstance(); // Class 객체를 이용한 객체 생성
```

---

## String 클래스

### 변경 불가능한(immutable) 클래스

한번 생성된 String 인스턴스가 갖고 있는 문자열은 읽어 올 수만 있고, 변경은 불가능하다.

```java
String a = "a";
String b = "b";
a = a+b;
```

위 코드와 같이 '+' 연산자를 이용해서 문자열을 결합하면 인스턴스 내의 문자열이 바뀌는 것이 아니라 새로운 문자열이 담긴 String 인스턴스가 생성된다.

이처럼 '+' 연산자를 사용해서 문자열을 결합하면 매 연산 시마다 새로운 문자열을 가진 String 인스턴스가 생성되어 메모리공간을 차지하게 되므로 가능한 결합 횟수를 줄이는 것이 좋다.

`문자열간의 결합이나 추출 등 문자열을 다루는 작업이 많이 필요할 때는 StringBuffer 클래스를 사용하자. StringBuffer 인스턴스에 저장된 문자열을 변경이 가능하므로 하나의 StringBuffer 인스턴스만으로 문자열을 다루는 것이 가능하다.`

<br>

### 문자열의 비교

**문자열을 만드는 두 가지 방법**

1. 문자열 리터럴을 지정
2. String 클래스의 생성자 사용

<br>

```java
String str1 = "abc"; // 문자열 리터럴 "abc"의 주소가 str1에 지정됨
String str2 = "abc"; // 문자열 리터럴 "abc"의 주소가 str2에 지정됨
String str3 = new String("abc"); // 새로운 String 인스턴스를 생성
String str4 = new String("abc"); // 새로운 String 인스턴스를 생성S
```

String 클래스의 생성자를 이용하면 -> new 연산자에 의한 메모리 할당 (항상 새로운 String 인스턴스 생성)

문자열 리터럴 -> 이미 존재하는 것을 재사용

<br>

### 문자열 리터럴

문자열 리터럴은 컴파일 시에 클래스 파일에 저장된다. 이 때 같은 내용의 문자열 리터럴은 한번만 저장된다.

<br>

### join()과 StringJoiner

join()은 여러 문자열 사이에 구분자를 넣어서 결합한다. / split()과 반대 작업

```java
String animals = "dog,cat,bear";
String[] arr = animals.split(","); // 문자열을 ','를 구분자로 나눠서 배열에 저장
String str = String.join("-", arr); // 배열의 문자열을 '-'로 구분해서 결합
System.out.println(str); // dog-cat-bear
```

<br>

```java
StringJoiner sj = new StringJoiner(",", "[", "]");
String[] strArr = {"aaa", "bbb", "ccc"};

for(String s : strArr)
  sj.add(s.toUpperCase());

System.out.println(sj.toString()); // [AAA,BBB,CCC]
```

<br>

### String.format()

```java
String str = String.format("%d 더하기 %d는 %d입니다.", 3, 5, 3+5);
System.out.println(str); // 3 더하기 5는 8입니다.
```

<br>

### 기본형 값을 String으로 변환

성능은 valueOf()가 더 좋지만, 빈 문자열을 더하는 방법이 간단하고 편하므로 성능향상이 필요한 경우에만 valueOf()를 사용하면 된다.

```java
int i = 100;
String str1 = i + ""; // 100을 "100"으로 변환하는 방법1
String str2 = String.valueOf(i); // 100을 "100"으로 변환하는 방법2
```

> 참조변수에 String을 더하면, 참조변수가 가리키는 인스턴스의 toString()을 호출하여 String을 얻은 다음 결합한다.

<br>

### String을 기본형 값으로 변환

```java
int i = Integer.parseInt("100"); // "100"을 100으로 변환하는 방법1
int i2 = Integer.valueOf("100"); // "100"을 100으로 변환하는 방법2
```

valueOf()의 반환타입은 Integer인데, 오토박싱에 의해 int로 자동 변환된다.

<br>

## StringBuffer클래스와 StringBuilder클래스

String 클래스: 인스턴스 생성 시 지정된 문자열 변경 불가능

StringBuffer 클래스 : 변경 가능, 내부적으로 문자열 편집을 위한 버퍼(buffer)를 가지고 있으며, StringBuffer 인스턴스 생성 시 크기 지정 가능

<br>
편집할 문자열의 길이를 고려하여 버퍼의 길이를 충분히 잡아주자. 편집 중인 문자열이 버퍼의 길이를 넘어서게 되면 버퍼의 길이를 늘려주는 작업이 추가적으로 수행되어야하므로 작업효율이 떨어진다.

<br>

StringBuffer 클래스는 String 클래스처럼 문자열을 저장하기 위한 char형 배열의 참조변수를 인스턴스 변수로 선언해 놓고 있다.

```java
public final class StringBuffer implements java.io.Serializable {
  private char[] value;
  ...
}
```

<br>

### StringBuffer의 생성자

StringBuffer 클래스의 인스턴스를 생성하면, 적절한 길이의 char형 배열이 생성되고, 이 배열은 문자열을 저장하고 편집하기 위한 공간(buffer)으로 사용된다.

StringBuffer(int length)를 사용해서 저장될 문자열의 길이를 고려하여 충분히 여유있는 크기로 지정한다. 크기를 따로 지정하지 않으면 16개의 문자를 저장할 수 있는 크기의 버퍼를 생성한다.

```java
public StringBuffer(int length){
  value = new char[length];
  shared = false;
}

public StringBuffer(){
  this(16);
}

public StringBuffer(String str) {
  this(str.length() + 16);
  append(str);
}
```

<br>

### StringBuilder

StringBuffer는 멀티쓰레드에 안전(thread safe)하도록 동기화되어 있다. (멀티쓰레드나 동기화에 대해서는 아직 다루지 않았으므로 동기화가 StringBuffer의 성능을 떨어뜨린다는 것만 우선 이해하자)

멀티쓰레드로 작성된 프로그램이 아닌 경우, StringBuffer의 동기화는 불필요하게 성능 저하를 유발한다.

<br>

StringBuffer에서 쓰레드의 동기화만 뺀 것이 StringBuilder이다.

<br>























