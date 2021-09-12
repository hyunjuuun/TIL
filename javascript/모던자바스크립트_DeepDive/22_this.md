# this 키워드


객체는 상태(state)를 나타내는 프로퍼티와 동작(behavior)를 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조다.

동작을 나타내는 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다.  이때 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 먼저 **자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.**

객체 리터럴 방식으로 생성한 객체의 경우 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다.

```jsx
const circle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  radius: 5,
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getDiameter() {
    // 이 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면
    // 자신이 속한 객체인 circle을 참조할 수 있어야 한다.
    return 2 * circle.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

하지만 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지 않으며 바람직하지도 않다. 생성자 함수 방식으로 인스턴스를 생성하는 경우를 생각해보자.

```jsx
function Circle(radius) {
  // 이 시점에서는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  ????.radius = radius;
}

Circle.prototype.getDiameter = function() {
  // 이 시점에서는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  return 2 * ????.radius;
};

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다.
const circle = new Circle(5);
```

생성자 함수를 정의하는 시점에서는 아직 인스턴스를 생성하기 이전이므로 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수 없다. 따라서 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요하다. 이를 위해 자바스크립트는 this라는 특수한 식별자를 제공한다.

**this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수(self-referencing variable)다. this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.**

**this가 가리키는 값, 즉 this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.**

### this 바인딩

> 바인딩이란 식별자와 값을 연결하는 과정을 의미한다. 예를 들어, 변수 선언은 변수 이름(식별자)과 확보된 메모리 공간의 주소를 바인딩하는 것이다. this 바인딩은 this(키워드로 분류되지만 식별자 역할을 한다)와 this가 가리킬 객체를 바인딩하는 것이다.

객체 리터럴의 메서드 내부에서의 this는 메서드를 호출한 객체를 가리킨다.

생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다. 이처럼 this는 상황에 따라 가리키는 대상이 다르다.

자바나 C++ 같은 클래스 기반 언어에서 this는 언제나 클래스가 생성하는 인스턴스를 가리킨다. 하지만 자바스크립트의 this는 함수가 호출되는 방식에 따라 this에 바인딩될 값, 즉 this 바인딩이 동적으로 결정된다. 또한 strict mode 역시 this 바인딩에 영향을 준다.

this는 코드 어디에서든 참조 가능하다.

- 전역에서 this는 전역 객체 window를 가리킨다
- 일반 함수 내부에서 this는 전역 객체 window를 가리킨다
- 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다
- 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다

하지만 this는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이므로 일반적으로 객체의 메서드 내부 또는 생성자 함수 내부에서만 의미가 있다. 따라서 strict mode가 적용된 일반 함수 내부의 this에는 undefined가 바인딩된다. 일반 함수 내부에서 this를 사용할 필요가 없기 때문이다.

# 함수 호출 방식과 this 바인딩


**this 바인딩(this에 바인딩될 값)은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.**

### 렉시컬 스코프와 this 바인딩은 결정 시기가 다르다.

> 함수의 상위 스코프를 결정하는 방식인 렉시컬 스코프(lexical scope)는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정한다. 하지만 this 바인딩은 함수 호출 시점에 결정된다.

주의할 것은 동일한 함수도 다양한 방식으로 호출할 수 있다는 것이다. 함수를 호출하는 방식은 다음과 같이 다양하다.

1. 일반 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

## 일반 함수 호출

기본적으로 this에는 전역 객체가 바인딩된다.

콜백 함수가 일반 함수로 호출된다면 콜백 함수 내부의 this에도 전역 객체가 바인딩된다. 어떠한 함수라도 일반 함수로 호출되면 this에 전역 객체가 바인딩된다.

## 메서드 호출

메서드 내부의 this에는 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩된다. 주의할 것은 메서드 내부의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩된다는 것이다.

```jsx
const person = {
	name: 'Lee',
	getName() {
		// 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다.
		return this.name;	
	}
};

// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()); // Lee
```

메서드는 프로퍼티에 바인딩된 함수다. 즉, person 객체의 getName 프로퍼티가 가리키는 함수 객체는 person 객체에 포함된 것이 아니라 독립적으로 존재하는 별도의 객체다. getName 프롶터ㅣ가 함수 객체를 가리키고 있을 뿐이다.

따라서 getName 프로퍼티가 가리키는 함수 객체, 즉 getName 메서드는 다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드가 될 수도 있고 일반 변수에 할당하여 일반 함수로 호출될 수도 있다.

```jsx
const anotherPerson = {
	name: 'Kim'
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며
// 기본값은 ''다.
// Node.js 환경에서 this.name은 undefined다.
```

따라서 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고 메서드를 호출한 객체에 바인딩된다.

프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 마찬가지로 해당 메서드를 호출한 객체에 바인딩된다.

## 생성자 함수 호출

생성자 함수 내부의 this에는 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩된다.

만약 new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수가 아니라 일반 함수로 동작한다.

## Function.prototype.apply/call/bind 메서드에 의한 간접 호출

apply, call, bind 메서드는 Function.prototype의 메서드다. 즉, 이들 메서드는 모든 함수가 상속받아 사용할 수 있다.

Function.prototype.apply, Function.prototype.call 메서드는 this로 사용할 객체와 인수 리스트를 인수로 전달받아 함수를 호출한다.

```jsx
// 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다.
// @param thisArg - this로 사용할 객체
// @param argsArray - 함수에게 전달한 인수 리스트의 배열 또는 유사 배열 객체
// @returns 호출된 함수의 반환값

Function.prototype.apply(thisArg[, argsArray])

// 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출한다.
// @param thisArg - this로 사용할 객체
// @param arg1, arg2, ... - 함수에게 전달한 인수 리스트
// @returns 호출된 함수의 반환값

Function.prototype.call(thisArg[, arg1[, arg2, ...]]])
```

```jsx
function getThisBinding() {
	return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

console.log(getThisBinding()); // window

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
console.log(getThisBinding.apply(thisArg)); // {a: 1}
console.log(getThisBinding.call(thisArg)); // {a: 1}
```

apply와 call 메서드의 본질적인 기능은 함수를 호출하는 것이다. apply와 call 메서드는 함수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩한다.

apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식만 다를 뿐 동일하게 동작한다.

Function.prototype.bind 메서드는 apply와 call 메서드와 달리 함수를 호출하지 않고 this로 사용할 객체만 전달한다.

bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용된다.

```jsx
const person = {
	name: 'Lee',
	foo(callback) {
		// 1
		setTimeout(callback, 100);
	}
};

person.foo(function() {
	console.log(`Hi! my name is ${this.name}.`); // 2
});
```

person.foo의 콜백 함수가 호출되기 이전인 1의 시점에서는 this는 foo 메서드를 호출한 객체, 즉 person 객체를 가리킨다. 그러나 person.foo의 콜백 함수가 일반 함수로서 호출된 2의 시점에서 this는 전역 객체 window를 가리킨다. 따라서 person.foo의 콜백 함수 내부에서 this.name은 window.name과 같다.

이때 위 예제에서 person.foo의 콜백 함수는 외부 함수 person.foo를 돕는 헬퍼 함수(보조 함수) 역할을 하기 때문에 외부 함수 person.foo 내부의 this와 콜백 함수 내부의 this가 상이하면 문맥상 문제가 발생한다. 따라서 콜백 함수 내부의 this를 외부 함수 내부의 this와 일치시켜야 한다. 이때 bind 메서드를 사용하여 this를 일치시킬 수 있다.

```jsx
const person = {
	name: 'Lee',
	foo(callback) {
		// bind 메서드로 callback 함수 내부의 this 바인딩을 전달
		setTimeout(callback.bind(this), 100);
	}
};

person.foo(function() {
	console.log(`Hi! my name is ${this.name}.`); // 2
});
```

정리

- 함수 호출 방식 → this 바인딩

일반 함수 호출 → 전역 객체

메서드 호출 → 메서드를 호출한 객체

생성자 함수 호출 → 생성자 함수가 (미래에) 생성할 인스턴스

Function.prototype.apply/call/bind 메서드에 의한 간접 호출 → Function.prototype.apply/call/bind 메서드에 첫 번째 인수로 전달한 객체
