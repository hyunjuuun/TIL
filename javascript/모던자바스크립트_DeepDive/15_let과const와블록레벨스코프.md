# var 키워드로 선언한 변수의 문제점


var 키워드로 선언한 변수는 다음과 같은 특징이 있다.

## 변수 중복 선언 허용

```jsx
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var x = 100;
// 초기화문이 없는 변수 선언문은 무시된다.
var y;

console.log(x); // 100
console.log(y); // 1
```

위 예제와 같이 만약 동일한 이름의 변수가 이미 선언되어 있는 것을 모르고 변수를 중복 선언하면서 값까지 할당했다면 의도치 않게 먼저 선언된 변수 값이 변경되는 부작용이 발생한다.

## 함수 레벨 스코프

var 키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역 스코프로 인정한다. 따라서 함수 외부에서 var 키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역 변수가 된다.

```jsx
var x = 1;

if (true) {
	var x = 10;
}

console.log(x); // 10
```

for 문의 변수 선언문에서 var 키워드로 선언한 변수도 전역 변수가 된다.

```jsx
var i = 10;

for (var i=0; i<5; i++) {
	console.log(i); // 0 1 2 3 4
}

console.log(i); // 5
```

## 변수 호이스팅

var 키워드로 변수를 선언하면 변수 호이스팅에 의해 변수 선언문이 스코프의 선두로 끌어 올려진 것처럼 동작한다. 즉, 변수 호이스팅에 의해 var 키워드로 선언한 변수는 변수 선언문 이전에 참조할 수 있다. 단, 할당문 이전에 변수를 참조하면 언제나 undefined를 반환한다.

```jsx
console.log(foo); // undefined

foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
var foo;
```

변수 선언문 이전에 변수를 참조하는 것은 변수 호이스팅에 의해 에러를 발생시키지는 않지만 프로그램의 흐름상 맞지 않을뿐더러 가독성을 떨어뜨리고 오류를 발생시킬 여지를 남긴다.

# let 키워드


## 변수 중복 선언 금지

let 키워드로 이름이 같은 변수를 중복 선언하면 문법 에러가 발생한다.

```jsx
let bar = 123;
// let 이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

## 블록 레벨 스코프

let 키워드로 선언한 변수는 모든 코드 블록(함수, if, for, while, try/catch문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```jsx
let foo = 1; // 전역 변수
{
	let foo = 2; // 지역 변수
	let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

## 변수 호이스팅

let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다.

```jsx
console.log(foo); // ReferenceError: foo is not defined
let foo;
```

**let 키워드로 선언한 변수는 '선언 단계'와 '초기화 단계'가 분리되어 진행된다.** 즉, 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 선언 단계가 먼저 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행된다.

만약 초기화 단계가 실행되기 이전에 변수에 접근하려고 하면 참조 에러가 발생한다. 스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간을 일시적 사각지대(Temporal Dead Zone; TDZ)라고 부른다.

```jsx
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

결국 let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 보인다. 하지만 그렇지 않다.

```jsx
let foo = 1; // 전역 변수
{
	console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
	let foo = 2; // 지역 변수
}
```

let 키워드로 선언한 변수의 경우 호이스팅이 발생하지 않는다면 위 예제는 전역 변수 foo의 값을 출력해야 한다.

## 전역 객체와 let

var 키워드로 선언한 전역 변수와 전역 함수, 선언하지 않은 변수에 값을 할당한 암묵적 전역은 전역객체 window의 프로퍼티가 된다. 전역 객체의 프로퍼티를 참조할 때 window를 생략할 수 있다.

```jsx
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // f foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // f foo() {}
```

let 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다. 즉, window.foo와 같이 접근할 수 없다. let 전역 변수는 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하게 된다.

```jsx
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

# const 키워드


const 키워드는 상수(constant)를 선언하기 위해 사용한다. 하지만 반드시 상수만을 위해 사용하지는 않는다. (후반부에 다시 설명)

const 키워드의 특징은 대부분 let 키워드와 동일하며 다른 점을 중심으로 살펴보자.

## 선언과 초기화

const 키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야 한다.

```jsx
const foo; // SyntaxError: Missing initializer in const declaration
```

const 키워드로 선언한 변수는 let 키워드로 선언한 변수와 마찬가지로 블록 레벨 스코프를 가지며, 변수 호이스팅이 발생하지 않는 것처럼 동작한다.

```jsx
{
	// 변수 호이스팅이 발생하지 않는 것처럼 동작한다.
	console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
	const foo = 1;
	console.log(foo); // 1
}

// 블록 레벨 스코프를 가진다.
console.log(foo); // ReferenceError: foo is not defined
```

## 재할당 금지

const 키워드로 선언한 변수는 재할당이 금지된다.

```jsx
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

## 상수

const 키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없다. 원시 값은 변경 불가능한 값이므로 재할당 없이 값을 변경할 수 있는 방법이 없기 때문이다. 이러한 특징을 이용해 const 키워드를 상수를 표현하는 데 사용하기도 한다.

**상수는 재할당이 금지된 변수를 말한다.** (상수도 값을 저장하기 위한 메모리 공간이 필요하므로 변수라고 할 수 있다.)

상수는 상태 유지와 가독성, 유지보수의 편의를 위해 적극적으로 사용해야 한다.

```jsx
// 세전 가격
let preTaxPrice = 100;

// 세후 가격
// 0.1의 의미를 명확히 알기 어렵기 때문에 가독성이 좋지 않다.
let afterTaxPrice = preTaxPrice + (preTaxPrice * 0.1);

console.log(afterTaxPrice); // 110
```

위 예제에서 0.1은 어떤 의미로 사용했는지 명확히 알기 어려우므로 가독성이 좋지 않다. 또한 세율을 의미하는 0.1은 쉽게 바뀌지 않는 값이며, 프로그램 전체에서 고정된 값을 사용해야 한다. 이때 세율을 상수로 정의하면 값의 의미를 쉽게 파악할 수 있고 변경될 수 없는 고정값으로 사용할 수 있다.

**const 키워드로 선언된 변수에 원시 값을 할당한 경우 원시 값은 변경할 수 없는 값이고 const 키워드에 의해 재할당이 금지되므로 할당된 값을 변경할 수 있는 방법은 없다.**

일반적으로 상수의 이름은 대문자로 선언해 상수임을 명확히 나타낸다. 여러 단어로 이뤄진 경우에는 언더스코어(_)로 구분해서 스네이크 케이스로 표현하는 것이 일반적이다.

```jsx
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice); // 110
```

## const 키워드와 객체

**const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다.** 변경 가능한 값인 객체는 재할당 없이도 직접 변경이 가능하기 때문이다.

```jsx
const person = {
	name: 'Lee'
};

// 객체는 변경 가능한 값이다. 따라서 재할당 없이 변경이 가능하다.
person.name = 'Kim';

console.log(person); // {name: "Kim"}
```

**const 키워드는 재할당을 금지할 뿐 '불변'을 의미하지 않는다.**

새로운 값을 재할당하는 것은 불가능하지만 프로퍼티 동적 생성, 삭제, 프로퍼티 값의 변경을 통해 객체를 변경하는 것은 가능하다.

# var vs. let vs. const


권장 사항

- ES6을 사용한다면 var 키워드는 사용하지 않는다.
- 재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
- 변경이 발생하지 않고 읽기 전용으로 사용하는(재할당이 필요 없는 상수) 원시 값과 객체에는 const 키워드를 사용한다. const 키워드는 재할당을 금지하므로 var, let 키워드보다 안전하다.
