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











































