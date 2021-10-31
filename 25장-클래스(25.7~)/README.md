# 25장 - 클래스(25.7~)

### 25.7.1 인스턴스 프로퍼티

---

- 인스턴스 프로퍼티는 contstructor 내부에서 정의해야 한다. 

- 생성자 함수에서 생성자 함수가 생성할 인스턴스의 프로퍼티를 정의하는 것과 마찬가지로 constructor 내부에서 this에 인스턴스 프로퍼티를 추가한다.

- ES6의 클래스는 다른 객체지향 언어처럼 `private`, `public`, `protected` 키워드와 같은 접근 제한자를 지원하지 않는다.

  - 따라서, 인스턴스 프로퍼티는 언제나 `public`하다.

  ```javascript
  class Person {
      constructor(name) {
          //인스턴스 프로퍼티
          this.name = name; // name 프로퍼티는 public하다.
      }
  }
  
  const me = new Person('Lee');
  console.log(me) // Person {name: 'Lee'}
  ```



<br>

### 25.7.2 접근자 프로퍼티

---

- 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티이다.

- 접근자 프로퍼티 또한 클래스에서도 사용할 수 있다.

  ```javascript
  class Person {
      constructor(firstName, lastName) {
          this.firstName = firstName;
          this.lastName = lastName;
      }
      
      // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
      // getter 함수
      get fullName()  {
          return `${this.firstName} ${this.lastName}`;
      }
      
      // setter 함수
      set fullName(name) {
          [this.firstName, this.lastName] = name.split(' ');
      }
  }
  
  const me = new Person('sanggil', 'Lee');
  
  // 접근자 프로퍼티를 통한 프로퍼티 값의 참조
  // 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
  console.log(me.fullName); // sanggil Lee
  
  // 접근자 프로퍼티를 통한 프로퍼티 값의 저장
  // 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
  me.fullName = 'sangmin song';
  console.log(me) // {firstName: 'sangmin', lastName: 'song'}
  ```

- 클래스의 메서드는 기본적으로 프로토타입 메서드가 된다.
  - 따라서, 클래스의 접근자 프로퍼티 또한 인스턴스 프로퍼티가 아닌 `프로토타입의 프로퍼티`가 된다.



<br>

### 25.7.3 클래스 필드 정의 제안

---

- 클래스 필드는 클래스 기반 객체지향 언어에서 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어이다.

- 자바스크립트의 클래스에서 인스턴스 프로퍼티를 선언하고 초기화하려면 반드시 `constructor` 내부에서 `this`에 프로퍼티를 추가해야한다.

  - 하지만, 자바의 클래스에서는 아래 예제와 같이 클래스 필드를 변수처럼 `클래스 몸체에` this 없이 선언한다. 

    ```java
    public class Person {
        // 1.클래스 필드는 클래스 몸체에 this 없이 선언해야한다.
        private String firstName = "";
    }
    ```

- 자바스크립트의 클래스에서 인스턴스 프로퍼티를 참조하려면 반드시 `this`를 사용하여 참조해야한다.

  - 하지만, 자바의 클래스에서는 `this`를 생략해도 클래스 필드를 참조할 수 있다. 

      ```java
      public String getFullName() {
          // 2. this 없이도 클래스 필드를 참조할 수 있다.
          return firstName + " " + lastName;
      }	
      ```

  - 또한 아래의 예제와 같이 클래스 기반 객체지향 언어의 `this`는 언제나 클래스가 생성할 인스턴스를 가리킨다.

    ```java
    // 생성자
    Person(String firstName, String lastName) {
        // 3. this는 언제나 클래스가 생성할 인스턴스를 가리킨다.
    	this.firstName = firstName;
        this.lastName = lastName;
    }
    ```


- 자바스크립트의 클래스 몸체에는 메서드만 선언할 수 있다. 따라서 클래스 몸체에 자바와 유사하게 클래스 필드를 선언하면 문법 에러가 발생한다.

- 클래스 몸체에서 클래스 필드를 정의하는 경우 `this`에 클래스 필드를 바인딩해서는 안된다. `this`는 클래스의 constructor와 메서드 내에서만 유효하다.

  ```javascript
  class Person {
      // this에 클래스 필드를 바인딩해서는 안 된다.
      this.name = ''; // SyntaxError: Unexpected token '.'
  }
  ```

- 클래스 필드를 참조하는 경우 자바와 같은 클래스 기반 객체지향 언어에서는 `this`를 생략할 수 있으나 자바스크립트에서는 this를 반드시 사용해야 한다.

  ```javascript
  class Person {
      // 클래스 필드
      name  'Lee';
  	constructor() {
          console.log(name); // ReferenceError: name is not defined
      }
  }
  
  new Person();
  ```

- 클래스 필드에 초기값을 할당하지 않으면 `undefined`를 갖는다.

  ```javascript
  class Person {
      // 클래스 필드를 초기화하지 않으면 undefined를 갖는다.
      name;
  }
  
  const me = new Person();
  console.log(me); // Person {name: undefined}
  ```

- 인스턴스를 생성할 때 외부의 초기값으로 클래스 필드를 초기화해야 할 필요가 있다면 `constructor`에서 클래스 필드를 초기화해야한다.

  ```javascript
  class Person {
      constructor(name) {
          // 클래스 필드 초기화
          this.name = name;
      }
  }
  
  const me = new Person('Lee');
  console.log(me); // Person {name: "Lee"}
  ```

- 함수는 일급 객체이므로 함수를 클래스 필드에 할당할 수 있다. 하지만, 클래스 필드에 함수를 할당하는 경우, 함수는 프로토타입 메서드가 아닌 `인스턴스 메서드`가 된다. 모든 클래스 필드는 `인스턴스 프로퍼티`가 되기 때문이다. 따라서, 클래스 필드에 함수를 할당하는 것은 권장하지 않는다.



#### 25.7.4 private 필드 제안

---

- 자바스크립트는 캡슐화를 완전하게 지원하지 않는다.

  - 따라서, 인스턴스 프로퍼티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있다. 즉, 언제나 `public`이다.

- 이를 위해, 2021년 1월 기준으로, private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다. 이에 대해 살펴보자

- `private` 필드의 선두에는 `#`을 붙여준다. `private` 필드를 참조할 때도 `#`을 붙여주어야 한다.

- `private` 필드는 클래스 내부에서만 참조할 수 있다. 다만, 접근자 프로퍼티를 통해 간접적으로 접근하는 방법은 유효하다.

- `private` 필드는 반드시 클래스 몸체에 정의해야한다. `private` 필드를 직접 constructor에 정의하면 에러가 발생한다.

  ```javascript
  class Person {
      // private 필드 정의
      #name = '';
      constructor(name) {
          this.#name = name;
      }
  
  	// name은 접근자 프로퍼티다.
  	get name() {
          // private 필드를 참조
          return this.#name.trim();
      }
  }
  
  const me = new Person('Lee');
  console.log(me.name); // Lee
  ```

<br>

#### 25.7.5 static 필드 정의 제안

---

- 클래스는 static 키워드를 사용하여 정적 메서드를 정의 할 수 있지만, static 키워드를 사용하여 정적 필드를 정의할 수는 없었다.

- 하지만, static public 필드,  static private 필드, static private 메서드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다.

  ```javascript
  class MyMath {
      // static public 필드 정의
      static PI = 22 /7;
  	// static private 필드 정의
  	static #num = 10;
      // static 메서드
      static increment() {
          return ++MyMath.#num;
      }
  }
  
  console.log(MyMath.PI); // 3.14....
  console.log(MyMath.increment()); // 11
  ```

  

<br>

### 25.8 상속에 의한 클래스 확장

#### 25.8.1 클래스 상속과 생성자 함수 상속

---

- 프로토타입 기반 상속은 프로토타입 체인을 통해 다른 객체의 자산을 상속받는 개념이지만, `상속에 의한 클래스 확장`은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것!
- 클래스와 생성자 함수는 `인스턴스`를 생설할 수 있는 함수라는 점에서 매우 유사하지만, 클래스는 상속을 통해 기존 클래스를 확장할 수 있는 문법이 기본적으로 제공되지만 생성자 함수는 그렇지 않다는 차이점이 존재한다.
- 클래스는 상속을 통해 다른 클래스를 확장할 수 있는 문법인 `extends` 키워드가 기본적으로 제공된다.

<br>

#### 25.8.2 extends 키워드

---

- 상속을 통해 클래스를 확장하려면 `extends` 키워드를 사용하여 상속받을 클래스를 정의한다.
- **상속을 통해 확장된 클래스** - 서브클래스 또는 자식 클래스라고 불림
- **서브클래스에게 상속된 클래스** - 수퍼클래스 또는 부모 클래스라고 불림
- `extends` 키워드의 역할은 수퍼클래스와 서브클래스 간의 상속 관계를 설정하는 것
  - 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다.
    - 이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.
- 클래스도 프로토타입을 통해 상속 관계를 구현한다.

<br>

#### 25.8.3 동적 상속

---

- `extends` 키워드는 클래스뿐만 아니라 생성자 함수를 상속받아 클래스를 확장할 수도 있다. 단, `extends` 키워드 앞에는 반드시 클래스가 와야한다.

- `extends` 키워드 다음에는 클래스뿐만 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다. 이를 통해 동적으로 상속받을 대상을 결정할 수 있다.

  ```javascript
  function Base1() {}
  
  class Base2 {}
  
  let condition = true;
  
  // 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
  class Derived extends (condition ? Base1 : Base2) {}
  
  const derived = new Derived();
  console.log(derived); // Derived {}
  
  console.log(derived instanceof Base1); // true
  console.log(derived instanceof Base2); // false
  ```

  

<br>

#### 25.8.4 서브클래스의 constructor

---

- 수퍼 클래스와 서브클래스 모두 `constructor`를 생략하면 빈 객체가 생성된다. 프로퍼티를 소유하는 인스턴스를 생성하려면 `constructor` 내부에서 인스턴스에 프로퍼티를 추가해야한다.

  ```javascript
  // 수퍼 클래스
  class Base {
      constructor() {}
  }
  
  // 서브 클래스
  class Derived extends Base {
      constructor(...args) { super(...args); }
  }
  
  const derived = new Derived();
  console.log(derived); // Derived {}
  ```

<br>

### 25.8.5 super 키워드

---

- super 키워드는 함수처럼 호출할 수도 있고 `this`와 같이 식별자처럼 참조할 수 있는 특수한 키워드다. 

<br>

#### super 호출

---

- super를 호출하면 수퍼클래스의 constructor를 호출한다.

- 만약, 수퍼클래스의 constructor 내부에서 추가한 프로퍼티를 그대로 갖는 인스턴스를 생성한다면 서브클래스의 `constructor`를 생략할 수 있다.

  - 이때, new 연산자와 함께 서브클래스를 호출하면서 전달한 인수는 모두 서브클래스에 암묵적으로 정의된 `constructor`의 super 호출을 통해 수퍼클래스의 `constructor`에 전달된다.

    ```javascript
    // 수퍼 클래스
    class Base {
        constructor(a, b) {
            this.a = a;
            this.b = b;
        }
    }
    
    // 서브 클래스
    class Derived extends Base {
        // 다음과 같이 암묵적으로 constructor가 정의된다.
        // constructor(...args) { super(...args); }
    }
    
    const derived = new Derived(1, 2);
    console.log(dervied); // Derived { a:1, b:2 }
    ```

<br>

- 위와 다르게, 수퍼클래스에서 추가한 프로퍼티와 서브클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브클래스의 `constructor`를 생략할 수 없다.

  - 이때, new 연산자와 함께 서브클래스를 호출하면서 전달한 인수 중에서 수퍼클래스의 constructor에 전달할 필요가 있는 인수는 서브클래스의 `constructor`에서 호출하는 super를 통해 전달된다.

    ```javascript
    class Base {
        constructro(a, b) {
            this.a = a;
            this.b = b;
        }
    }
    
    // 서브 클래스
    class Derived extends Base {
        constructor(a, b, c) {
            super(a, b);
            this.c = c;
        }
    }
    
    const derived = new Derived(1, 2, 3);
    console.log(derived); // Derived {a : 1, b: 2, c: 3}
    ```

<br>

- `super`를 호출할 때 주의할 사항들은 다음과 같다.
  - 서브클래서에서 `constructor`를 생략하지 않는 경우 서브클래스와 constructor에서는 반드시 super를 호출해야한다.
  - 서브클래스의 `constructor`에서 super를 호출하기 전에는 this를 참조할 수 없다.
  - super는 반드시 서브클래스의 `constructor`에서만 호출한다. 서브클래스가 아닌 클래스의 constructor나 함수에서 super를 호출하면 에러가 발생한다.

<br>

####  super 참조

---

- 메서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

  - 서브클래스의 프로토타입 메서드 내에서 super.sayHi는 수퍼클래스의 프로토타입 메서드 sayHi를 카리킨다.

    ```javascript
    // 수퍼클래스
    class Base {
        constructor(name) {
            this.name = name;
        }
        
        sayHi() {
            retunr `Hi ${this.name}`;
        }
    }
    
    // 서브클래스
    class Derived extends Base {
        sayHi() {
            // super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.
            return `${super.sayHi()}`
        }
    }
    ```
    - super 참조를 통해 수퍼클래스의 메서드를 참조하려면 super가 수퍼클래스의 메서드가 바인딩된 객체, 즉 수퍼클래스의 prototype 프로퍼티에 바인딩된 프로토타입을 참조할 수 있어야한다.

  - 서브 클래스의 정적 메서드는 수퍼클래스의 정적 메서드를 가리킨다.

<br>

#### 25.8.6 상속 클래스의 인스턴스 생성 과정

#### 1. 서브클래스의 super 호출

---

- 자바스크립트 엔진은 클래스를 평가할 때 수퍼클래스와 서브클래스를 구분하기 위해 'base' 또는 'derived'를 값으로 갖는 내부 슬롯 [[ConstructorKind]]를 갖는다.
- 다른 클래스를 상속받지 않는 클래스(그리고 생성자함수)는 new 연산자와 함께 호출되었을 때 암묵적으로 빈 객체, 즉 인스턴스를 생성하고 이를 `this`에 바인딩한다.
- 하지만 서브클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 인스턴스 생성을 위임한다.
  - 이것이 바로 서브클래스의 `constructor`에 반드시 super를 호출해야하는 이유다.
  - 서브클래스가 new 연산자와 함께 호출되면 서브클래스 constructor 내부의 super 키워드가 함수처럼 호출된다. -> super가 호출되면 수퍼클래스의 `constructor`가 호출된다.

<br>

#### 2. 수퍼클래스의 인스턴스 생성과 this 바인딩

---

- 수퍼클래스의 constructor 내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성한다. 이 빈 객체가 바로 클래스가 생성한 인스턴스다.
- 그리고 암묵적으로 생성된 빈 객체, 즉 인스턴스는 `this`에 바인딩된다. -> 따라서 수퍼클래스의 constructor 내부의 `this`는 생성된 인스턴스를 가리킨다.
- 이때, 인스턴스는 수퍼클래스가 생성한 것이지만, new 연산자와 함께 호출된 클래스가 서브클래스이기 때문에 호출된 함수를 가리키는 `new.target`이 서브클래스를 가리킨다.
  - 따라서, 인스턴스는 `new.target`이 가리키는 서브클래스가 생성한 것으로 처리된다.
  - 최종적으로, 생성된 인스턴스의 프로토타입은 서브클래스의 `prototype` 프로퍼티가 가리키는 객체다.

<br>

#### 3. 수퍼클래스의 인스턴스 초기화

---

- 수퍼클래스의 `constructor`가 실행되어 `this`에 바인딩 되어 있는 인스턴스를 초기화한다.
- 즉, this에 바인딩 되어 있는 인스턴스에 프로퍼티를 추가하고 `constructor`가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

<br>

#### 4. 서브클래스 constructor로의 복귀와 this 바인딩

---

- super의 호출이 종료되고 제어 흐름이 서브클래스 `constructor`로 돌아온다. 이때 super가 반환한 인스턴스가 `this`에 바인딩된다.
  - 서브클래스는 별도의 인스턴스를 생성하지 않고 super가 반환한 인스턴스를 `this`에 바인딩하여 그대로 사용한다.

<br>

#### 5. 서브클래스의 인스턴스 초기화

---

- super 호출 이후, 서브클래스의 constructor에 기술되어 있는 인스턴스 초기화가 실행된다.

<br>

#### 6. 인스턴스 반환

---

- 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.
