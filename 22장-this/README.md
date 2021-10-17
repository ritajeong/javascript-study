# 22장 this

[참고영상](https://youtu.be/7RiMu2DQrb4)

## 22.1 this키워드

<br>

- 자바스크립트의 일반적인 함수들은 this라는 변수를 가지고 있다.
- this는 해당 함수를 메서드로 가지고 있는 객체 또는 해당 함수가 생성할 객체를 참조하는 변수이다.
- this는 함수 내에서 쓰일 때 의미가 있지만 전역에서도 참조할 수는 있다.
- this를 전역에서 참조할 경우 다음과 같이 동작한다.
  - 브라우저에서 동작할 경우 this는 전역 객체인 window를 참조한다.
  - node REPL에서 this는 전역 객체인 global을 참조한다.
  - commonJS의 모듈 시스템을 따르는 node 환경에서는 module.exports를 의미한다.
  - 브라우저 환경일 때, strict mode라면 undefined가 할당된다.

<br>

## 22.2 함수 호출 방식과 this 바인딩

<br>

자바스크립트에서 this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.

<br>

1. 일반함수 호출

     -  일반함수 호출이란 함수를 참조하고 있는 식별자에 괄호 연산자를 붙여 호출하는 방식을 의미한다.
        ```js
        // 다음의 경우 모두 일반함수 호출에 해당한다.
        function fn1() {}
        fn1(); 

        const fn2 = function() {};
        fn2();

        const obj = { method: function() {} };
        const fn3 = obj.method;
        fn3();

        // 특히 fn3의 경우 처럼 객체의 메서드를 다른 식별자에 할당만 했음에도 
        // 단지 호출 방식에 따라 일반함수 호출로 분류된다.
        ```
    - 일반 함수 호출 시 기본적으로 this에 전역 객체가 바인딩 된다.
        - 브라우저 환경에서는 window, node 환경에서는 global이 바인딩된다.
    - 단, strict mode일 경우에는 undefined가 할당된다.
        - node REPL에서는 그대로 전역객체가 바인딩된다.

2. 메서드 호출
     - 점 연산자를 통해 객체의 메서드로써 호출되는 함수의 내부에서 this는 점 연산자 앞에 있는 객체에 바인딩된다.
     - 이를 암묵적 바인딩이라고 부른다.

3. 생성자 함수 호출
     - new 연산자와 함께 호출되는 경우 함수는 생성자로써 호출된다.
     - 생성자 함수 내부에서 this는 생성할 객체를 의미한다.
     - 생정자 함수의 동작을 간단히 나타내면 다음과 같다.
        ```js
        function Func() {
          this.a = 1;
        }

        // 위 함수가 생성자 함수로 호출된 경우 다음과 같은 일들이 암묵적으로 일어난다.

        function Func() {
          // 생성될 객체에 this를 바인딩
          this = {};

          // prototype 연결

          // 함수 실행
          this.a = 1;

          // this를 반환
          return this;
        }
        ```

4. Function 객체의 메서드에 의한 호출
     - Function 객체는 this를 명시적으로 바인딩할 수 있는 메서드들을 제공한다.
     - apply, call
       - apply와 call 메서드는 this를 특정 객체에 바인딩 한 채로 함수를 호출한다.
       - 첫번째 인자에 this가 가리킬 객체를 넣고 두번째부터는 함수에게 전달될 인수를 넣는다.
       - apply와 call은 함수의 인수를 전달하는 방식에서만 차이를 갖는다.
          ```js
          function testBinding(...args) {
            console.log(this, args);
          }

          const obj = { a: 1 };

          console.log(getThis.apply(obj, [1, 2, 3])); // { a: 1 }, [1,2,3]
          console.log(getThis.call(obj, 1, 2, 3); // { a: 1 }, [1,2,3]

          // apply는 배열 혹은 유사 배열로 인수를 전달한다.
          // call은 쉼표로 구분해 인수를 각각 전달한다.
          ```
      - bind
        - bind 메서드는 this바인딩이 '고정' 되어있는 함수를 호출한다.
        - bind에 의해 생성된 함수는 일반 함수 호출 시에도 고정된 this바인딩을 보장 받는다.
        - bind 메서드에 전달한 두번째 이후 인수들은 고정된 인수도 여겨진다.
          ```js
          function testBinding(...args) {
            console.log(this, args);
          }

          const obj = { a: 1 };
          const boundObj = testBinding.bind(obj, 1, 2);

          boundObj(3); // { a: 1 } [ 1, 2, 3 ]
          ```

<br>

          > bind 메서드의 리턴값<br>
          > bind 메서드가 리턴하는 객체를 두고 편의상 함수라 표현하긴 했지만 사실 단순히 '함수'라고 이야기할 수는 없다. 자바스크립트 명세상 bind메서드가 반환하는 객체는 bound function exotic object라고 하는 특수 객체이다. 이 객체는 일반 함수의 프로퍼티와 내부 슬롯에 더해 실제 호출할 함수를 의미하는 [[BoundTargetFunction]], 고정된 this와 인수를 의미하는 [[BoundThis]], [[BoundArguments]]를 가지고 있다. 함수의 프로퍼티와 내부 슬롯을 모두 가지고 있는데 왜 함수라고 불릴 수는 없을까? 이 특수 객체는 [[Call]] 메서드를 구현하고 있기 때문에 callable이긴하나 호출 방식에 있어 일반 함수 다르다. [[Call]] 메서드 호출 시 실제로 실행되는 코드는 [[BoundTargetFunction]]의 내용이며 이때 [[BoundThis]]와 [[BoundArguments]]가 사용된다. 자세한 내용은 [여기](https://tc39.es/ecma262/#sec-bound-function-exotic-objects)를 참고하자

<br>

## 추가 내용1: this 바인딩 우선순위

<br>

살펴본 바와 같이 자바스크립트에서 this는 각 상황에 맞게 다양한 방식으로 바인딩된다. 그리고 이러한 바인딩 방식에는 우선순위가 존재한다. this의 바인딩 우선 순위는 다음과 같다.

`new 연산자에 의한 바인딩 > 명시적 바인딩 > 암시적 바인딩 > 일반함수 호출에 의한 바인딩`

그리고 이때 bind메서드에 의한 바인딩이 apply, call에 의한 바인딩 보다 우선순위가 높다. 조금 더 정확하게 말하면 apply, call, bind 실행시 이를 호출한 함수가 화살표 함수이거나 bound function exotic object이면 첫번째인자인 thisArg가 무시된다.

<br>

## 추가 내용2: 화살표 함수와 this

<br>

화살표함수는 this 키워드에 해당하는 값을 자기 스코프 내에 정의하지 않는다. 따라서 화살표 함수 내에서 this를 참조하게 되면 스코프 체인상 상위 스코프를 탐색한다.

<br>

## 추가 내용3: 콜백과 this

<br>

함수가 다른 함수의 인수, 그리나까 콜백으로 전달되면 그 함수는 일반 함수로써 호출되리라 기대된다. 그러나 이는 그 함수를 사용하는 함수의 정책에 의존한다. 자바스크립트의 몇몇 내장 메서드들은 콜백함수의 명시적 this 바인딩에 대한 정책을 가지고 있기도 하다. 예를 들어 addEventListener 메서드의 경우 이벤트 핸들러 인자에 해당하는 콜백의 this로 `currentTarget`을 바인딩하며 배열의 내장 메서드들의 경우 두번째 인자를 통해 콜백의 this에 바인딩할 객체를 받기도 한다.

<br>