# Symbol

## 33.1 심벌이란?

<br>

심벌(Symbol)은 ES6에서 추가된 원시타입으로 모든 심벌 타입의 값은 다른 값과 중복되지 않는 유일한 값으로 취급된다.

<br>

## 33.2 심벌 값의 생성

<br>

1. Symbol 함수를 통한 생성
   - 심벌 값은 기본적으로 Symbol 함수로 생성할 수 있다.
      ```js
      Symbol([description])
      // description: string
      ```
   - 심벌 값을 정의하는 리터럴은 존재하지 않는다.
   - Symbol 함수는 new 키워드를 통해 호출할 수 없다.
   - Symbol 함수에는 문자열 인수를 선택적으로 전달 할 수 있다. 이때 전달한 문자열은 심벌 값에 대한 설명이며 디버깅 용도로 사용된다. 주의할 점은 해당 설명이 심벌 값 자체에는 영향을 주지 않는다는 것이다. 즉, 같은 문자열은 인수로 전달해 생성한 심벌 값들이라도 각각 유일하다.
   - Symbol 생성 시 인수로 전달한 설명은 description 프로퍼티로 참조할 수 있다. 이때, 다른 원시타입처럼 래퍼객체를 생성하여 Symbol.prototype의 프로퍼티를 참조한다.
      ```js
      const mySymbol = Symbol("이건 내 심벌이야");

      console.log(mySymbol.description); // 이건 내 심벌이야
      ```
   - 심벌은 문자열이나 숫자로 암묵적 형변환되지 않는다. 다만 Symbol.prototype에 toString 메서드를 가지고 있다.
   - 불리언 타입으로는 암묵적으로 형변환 될 수 있다.

<br>

2. Symbol.for / Symbol.keyFor 메서드
   - 전역 심벌 레지스트리는 문자열 키와 심벌 값의 쌍을 저장하는 객체이다.
      - 이를 통해 유일한 심벌 값을 전역에서 공유할 수 있다.
   - Symbol.for과 Symbol.keyFor을 통해 전역 심벌 레지스트리에 접근한다.
   - Symbol.for(key: string)
      - Symbol.for에는 인수로 문자열을 전달한다.
      - 인수로 전달한 문자열을 키로하는 심벌 값이 전역 심벌 레지스트리에 있으면 해당 값을 반환한다.
      - 키와 일치하는 값이 없으면 해당 문자열을 키로하는 새로운 심벌 값을 생성하고 이를 반환한다.
   - Symbol.keyFor(value: Symbol)
      - Symbol.keyFor에는 인수로 심벌을 전달한다.
      - 인수로 받은 심벌이 전역 심벌 레지스트리에 저장되어 있으면 해당하는 심벌의 키를 반환한다.

<br>

## 33.4~6 심벌과 프로퍼티

<br>

- 객체의 프로퍼티 키로 심벌을 사용할 수 있다.
- 심벌은 유일한 값이므로 프로퍼티의 키로 사용하면 다른 프로퍼티 키와 충돌하지 않는다.
- 키가 심벌인 프로퍼티는 for ...in 문이나 Object.keys, Object.getOwnPropertyNames로 찾을 수 없다. 따라서 심벌을 이용해 프로퍼티를 은닉할 수 있다.
- 단, ES6에 도입된 Object.getOwnPropertySymbols 메서드를 사용하면 키가 심벌인 프로퍼티를 조화할 수 있다.
- 표준 빌트인 객체에 메서드를 추가할 때 심벌을 키로 사용하면 보다 안전하게 활용할 수 있다.

<br>

## 33.7 Well-known Symbol

- 자바스크립트에는 기본적으로 제공하는 빌트인 심벌 값이 있다. 이를 Well-known Symbol이라고 부른다.
- Well-known Symbol은 자바스크립트 엔진의 내부 알고리즘에 사용된다.
- 예를 들어 Symbol.iterator는 이터레이터를 반환하는 메서드의 키로 사용되는 심벌이다.