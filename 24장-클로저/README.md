# 24. 클로저

- 클로저는 자바스크립트 고유의 개념이 아니다. 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어(Haskell, Lisp, Scala) 등에서 사용되는 중요한 특성이다.

- 클로저는 자바스크립트 고유의 개념이 아니기 때문에 ECMAScript 사양에 등장하지 않는다.

- MDN에서는 클로저를 다음과 같이 정의하고 있다.

  > A closure is the combination of a function and the lexical environment within which that function was declared
  >
  > > 한국어 번역
  > >
  > > 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

- 위  정의는 난해해서 잘 와 닿지 않습니다. 위 정의에서 먼저 이해해야 할 핵심 키워드는 **"함수가 선언된 렉시컬 환경"** 입니다.

```js
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();
```

- 위 예제에서 innerFunc의 상위 스코프는 outerFunc의 스코프입니다. 따라서 innerFunc에서 자신을 포함하고 있는 outerFunc의 x 변수에 접근할 수 있습니다.
- 만약 innerFunc 함수가 outerFunc 함수의 내부에 정의되어 있지 않다면 innerFunc를 outerFunc 내부에서 호출하더라도 outerFunc의 변수에 접근할 수 없습니다.

```js
const x = 1;

function outerFunc() {
  const x = 10;
  innerFunc();
}

function innerFunc() {
  console.log(x); // 1
}

outerFunc();
```

- 이 같은 현상이 발생하는 이유는 자바스크립트가 렉시컬 스코프를 따르는 프로그래밍 언어이기 때문입니다.

## 24.1 렉시컬 스코프

- **자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다. 이를 렉시컬 스코프(정적 스코프)라고 한다.**

```js
const x = 1;

function foo() {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // 1
bar(); // 1
```

- 23장 "실행 컨텍스트"에서 살펴보았듯이 스코프의 실체는 실행 컨텍스트의 렉시컬 환경이다. 이 렉시컬 환경은 자신의 "외부 렉시컬 환경에 대한 참조"를 통해 상위 렉시컬 환경과 연결된다. 이것이 바로 스코프 체인이다.
- 따라서 "함수의 상위 스코프를 결정한다"는 것은 "렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값을 결정한다"는 것과 같다.
- 이 개념을 반영해서 다시 한번 렉시컬 스코프를 정의하면 다음과 같다. **렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 저장할 참조값, 즉 상위스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경(위치)에 의해 결정된다. 이것이 바로 렉시컬 스코프다.**

## 24.2 함수 객체의 내부 슬롯 [[Environment]]

- 함수가 정의된 환경과 호출되는 환경은 다를 수 있다. 따라서 렉시컬 스코프가 가능하려면 함수는 자신이 호출되는 환경과는 상관없이 자신이 정의된 환경, 즉 상위 스코프를 기억해야 한다.
- 이를 위해 **함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.**
- **함수 객체의 내부 슬롯 [[Environment]]에 저장된 현재 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 바로 상위스코프다. 또한 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 저장될 참조값이다. 함수 객체는 내부 슬롯 [[Environment]]에 저장한 렉시컬 환경의 참조, 즉 상위 스코프를 자신이 존재하는 한 기억한다.**

```js
const x = 1;

function foo() {
  const x = 10;

  // 상위 스코프는 함수 정의 환경(위치)에 따라 결정된다.
  // 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
  bar();
}

// 함수 bar는 자신의 상위 스코프, 즉 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
function bar() {
  console.log(x);
}

foo(); // 1
bar(); // 1
```

- 함수가 호출되면 함수 내부로 코드의 제어권이 이동한다. 그리고 함수 코드를 아래 순서로 평가하기 시작한다.

  1. 함수 실행컨텍스트 생성

  2. 함수 렉시컬 환경 생성

     2-1. 함수 환경 레코드 생성

     2-2. this 바인딩

     2-3. 외부 렉시컬 환경에 대한 참조 결정

- 이때 함수 렉시컬 환경의 구성 요소인 **외부 렉시컬 환경에 대한 참조에는 함수 객체의 내부 슬롯 [[Environment]]에 저장된 렉시컬 환경의 참조가 할당된다.**

- 즉, 함수 객체의 내부 슬롯 [[Environment]]에 저장된 렉시컬 환경의 참조는 바로 함수의 상위 스코프를 의미한다. 이것이 바로 함수 정의 위치에 따라 상위 스코프를 결정하는 렉시컬 스코프이다.

## 24.3 클로저와 렉시컬 환경

```js
const x = 1;

// ①
function outer() {
  const x = 10;
  const inner = function () { console.log(x); }; // ②
  return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 팝되어 제거된다.
const innerFunc = outer(); // ③
innerFunc(); // ④ 10
```

- **외부 함수보다 중첩 함수가 더 오래 유지 되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 클로저라고 부른다.**
- 자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저다. 하지만 일반적으로 모든 함수를 클로저라고 하지는 않는다.

```js
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;
      const y = 2;

      // 일반적으로 클로저라고 하지 않는다.
      function bar() {
        const z = 3;

        debugger;
        // 상위 스코프의 식별자를 참조하지 않는다.
        console.log(z);
      }

      return bar;
    }

    const bar = foo();
    bar();
  </script>
</body>
</html>
```

- 위 예제에서 중첩 함수 bar는 외부 함수 foo보다 오래 유지되지만 상위 스코프의 어떤 식별자도 참조하지 않는다.
- 이런 경우 대부분의 모던 브라우저는 최적화를 통해 상위 스코프를 기억하지 않는다. 따라서 bar함수는 클로저라고 할 수 없다.

```js
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;

      // 일반적으로 클로저라고 하지 않는다.
      // bar 함수는 클로저였지만 곧바로 소멸한다.
      function bar() {
        debugger;
        // 상위 스코프의 식별자를 참조한다.
        console.log(x);
      }
      bar();
    }

    foo();
  </script>
</body>
</html>
```

- 위 예제에서 bar는 상위 스코프를 참조하고 있다. 하지만 외부 함수 foo의 외부로 중첩된 bar가 반환되지 않는다. 즉, 외부 함수 foo보다 중첩함수 bar의 생명 주기가 짧다. 이런 경우 bar는 외부 함수보다 일찍 소멸되기 때문에 생명 주기가 종료된 외부 함수의 식별자를 참조할 수 있다는 클로저에 부합하지 않는다.
- 따라서 중첩 함수 bar는 일반적으로 클로저라고 하지 않는다.

```js
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;
      const y = 2;

      // 클로저
      // 중첩 함수 bar는 외부 함수보다 더 오래 유지되며 상위 스코프의 식별자를 참조한다.
      function bar() {
        debugger;
        console.log(x);
      }
      return bar;
    }

    const bar = foo();
    bar();
  </script>
</body>
</html>
```

- 위 예제의 중첩 함수 bar는 상위 스코프의 식별자를 참조하고 있으므로 클로저다. 
- 이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료된 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 클로저라고 부른다.
- **클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적이다.**

## 24.4 클로저의 활용

- **클로저는 상태를 안전하게 변경하고 유지하기 위해 사용한다.**
- 상태가 의도치 않게 변경되지 않도록 **상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용** 하기 위해 사용된다.

```js
// 카운트 상태 변수
let num = 0;

// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태를 1만큼 증가 시킨다.
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

- 위 코드에서 카운트 상태는 전역 변수를 통해 관리되고 있기 때문에 언제든지 누구나 접근할 수 있고 변경할 수 있다.

```js
// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태 변수
  let num = 0;

  // 카운트 상태를 1만큼 증가 시킨다.
  return ++num;
};

// 이전 상태를 유지하지 못한다.
console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1
```

- 카운트 상태를 안전하게 변경하기 위해 num을 increase함수의 지역 변수로 변경하였다.
- 하지만 increase 함수가 호출될 때마다 지역 변수 num은 0으로 초기화되기 때문에 출력 결과는 언제나 1이 나온다.

```js
// 카운트 상태 변경 함수
const increase = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저
  return function () {
    // 카운트 상태를 1만큼 증가 시킨다.
    return ++num;
  };
}());

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

- 위 코드를 실행하면 즉시 실행 함수가 호출되고 즉시 실행 함수가 반환한 함수가 increase 변수에 할당된다. Increase 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 스코프인 즉시 샐항 함수의 렉시컬 환경을 기억하는 클로저다.
- num변수는 외부에서 직접 접근할 수 없는 은닉된 private 변수이므로 전역 변수를 사용했을 떄와 같이 의도되지 않는 변경을 걱정할 필요가 없어 더 안정적인 프로그래밍이 가능하다.
- **이처럼 클로저는 상태가 의도치 않게 변경되지 않도록 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 변경하고 유지하기 위해 사용된다.**

## 24.5 캡슐화와 정보 은닉

- **캡슐화**는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다. 캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 **정보 은닉** 이라고 한다.
- 정보 은닉은 외부에 공개할 필요가 없는 구현의 일부를 외부에 공개되지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 정보를 보호하고, 객체 간의 상호 의존성, 즉 결합도를 낮추는 효과가 있다.

```js
function Person(name, age) {
  this.name = name; // public
  let _age = age;   // private

  // 인스턴스 메서드
  this.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };
}

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

- 위 예제에서 _age 변수는 Person 생성자 함수 외부에서 참조하거나 변경할 수 없다. 즉, _age 변수는 private하다.
- 하지만 위 예제에서 sayHi 메서드는 인스턴스 메서드이므로 Person 객체가 생성될 때마다 중복 생성된다. sayHi 메서드를 프로토타입 메서드로 변경하면 sayHi메서드의 중복 생성을 방지할 수 있다.

```js
function Person(name, age) {
  this.name = name; // public
  let _age = age;   // private
}

// 프로토타입 메서드
Person.prototype.sayHi = function () {
  // Person 생성자 함수의 지역 변수 _age를 참조할 수 없다
  console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
};
```

- 이때 Person.prototype.sayHi 메서드에서 Person 생성자 함수의 지역 변수 _age를 참조할 수 없는 문제가 발생한다.
- 다음과 같이 즉시 실행 함수를 사용하여 Person 생성자 함수와 Person.prototype.sayHi 메서드를 하나의 함수로 모아서 이를 해결할 수 있다.

```js
const Person = (function () {
  let _age = 0; // private

  // 생성자 함수
  function Person(name, age) {
    this.name = name; // public
    _age = age;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };

  // 생성자 함수를 반환
  return Person;
}());

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

- Person 생성자 함수와 sayHi 메서드는 이미 종료되어 소멸한 즉시 실행 함수의 지역 변수 _age를 참조할 수 있는 클로저다.

- 하지만 위 코드도 문제가 있다. Person 생성자 함수가 여러 개의 인스턴스를 생성할 경우 다음과 같이 _age 변수의 상태가 유지되지 않는다.

```js
const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.

// _age 변수 값이 변경된다!
me.sayHi(); // Hi! My name is Lee. I am 30.
```

- 이는 Person.prototype.sayHi 메서드가 단 한 번 생성되는 클로저이기 때문에 발생하는 현상이다.
- 이처럼 자바스크립트는 정보 은닉을 완전하게 지원하지 않는다.

> 현재 TC39 프로세스의 stage 3 에 클래스 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다.

## 24.6 자주 발생하는 실수

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () { return i; }; // ①
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // ②
}
```

- 위 예제는 클로저를 사용할 때 자주 발생하는 실수이다.

- (1)에서 함수가 funcs 배열의 요소로 추가된다. 그리고 (2)에서 함수를 호출해 0, 1, 2를 반환할 것으로 기대했다면 아쉽지만 그렇지 않다.
- for 문의 변수 선언문에서 var키워드로 선언한 i는 블록 레벨 스코프가 아니라 함수레벨 스코프를 갖기 때문에 전역 변수다.
- 전역 변수 i에는 0, 1, 2가 순차적으로 할당된다. 따라서 funcs 배열의 요소로 추가한 함수를 호출하면 전역 변수 i를 참조하여 i의 값 3이 출력된다.
- 클로저를 사용해 다음과 같이 바르게 동작하도록 변경할 수 있다.

```js
var funcs = [];

for (var i = 0; i < 3; i++){
  funcs[i] = (function (id) { // ①
    return function () {
      return id;
    };
  }(i));
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}	
```

- (1)에서 즉시 실행 함수는 전역 변수 i에 현재 할당되어 있는 값을 인수로 받아 id에 할당한 후 종료된다.
- 즉시 실행 함수가 반환한 중첩 함수는 자신의 상위 스코프를 기억하는 클로저이고, 매개변수 id는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유 변수가 되어 그 값이 유지된다.
- 위 예제는 var키워드로 선언한 변수가 전역 변수가 되기 때문에 발생하는 현상이다. let 키워드를 사용하면 이 같은 번거로움이 해결된다.

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () { return i; };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
}
```

