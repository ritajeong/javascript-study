# 16장 프로퍼티 어트리뷰트

## 16.1 내부 슬롯과 내부 메서드

#### 내부 슬롯과 내부 메서드는

- 자바스크립트 엔진의 내부 로직이다. 
  - 따라서 개발자가 직접 접근하거나 호출할 수 없다.
  - 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공한다. 
  - 예시) 모든 객체는 [[Prototype]]이라는 내부 슬롯을 가지며 _ _ proto _ _ 를 통해 간접적으로 접근할 수 있다.
  - (실제 코드에서 사용할 때는 __ 언더스코어 두 개를 붙여서 사용합니다. 마크다운 양식에 의해 불가피하게 공백을 넣어서 작성했습니다.)
- 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티와 의사 메서드다. 
- ECMAScript 사양에서 이중 대괄호([[...]])로 감싼 이름들이다. 

# 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

- 자바스크립트 엔진은 프로퍼티를 생성할 때 **프로퍼티의 상태**를 나타내는 **프로퍼티 어트리뷰트**를 기본값으로 자동 정의한다. 

- **프로퍼티의 상태** : 프로퍼티의 값(value), 값의 갱신 가능 여부(writable), 열거 가능 여부(enumerable), 재정의 가능 여부(configurable)

- **프로퍼티 어트리뷰트** : 자바스크립트 엔진이 관리하는 내부 상태 값인 내부 슬롯이다.

  - [[Value]], [[Writable]], [[Enumerable]]], [[Configurable]]
  - 직접 접근 불가능. 
  - Object.getOwnPropertyDescriptor 메서드로 간접 확인 가능

- #### Object.getOwnPropertyDescriptor

  - 첫번째 매개변수에는 객체의 참조를 전달
  - 두번째 매개변수에는 프로퍼티 키를 문자열로 전달
  - 이 때, Object.getOwnPropertyDescriptor 메서드는 프로퍼티 어트리뷰트 정보를 제공하는 '**프로퍼티 디스크립터 객체**'를 반환한다.
    만약 존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 프로퍼티 디스크립터를 요구하면 undefined가 반환된다.

```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}

/*
첫번째 매개변수에는 객체의 참조를 전달 - person
두번째 매개변수에는 프로퍼티 키를 문자열로 전달 - 'name'
*/
```

- #### Object.getOwnPropertyDescriptors

  - ES8에서 도입됨
  - 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다. 

```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

// 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```

## 16.3 데이터 프로퍼티와 접근자 프로퍼티

프로퍼티는 데이터 프로퍼티와 접근자 프로퍼티로 구분가능

- 데이터 프로퍼티 : 키와 값으로 구성된 일반적인 프로퍼티.
- 접근자 프로퍼티 : 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수(accessor function)로 구성된 프로퍼티다.

### 16.3.1 데이터 프로퍼티

- 데이터 프로퍼티는 다음과 같은 어트리뷰트를 갖는다.
- 이 프로퍼티 어트리뷰트는 자바스크립트 엔진이 프로퍼티를 생성할 때 기본값으로 자동 정의 된다.

---

#### 데이터 프로퍼티의 프로퍼티 어트리뷰트

- **[[Value]]**
  - 프로퍼티 디스크립터 객체의 프로퍼티 : value
  - 프로퍼티 키를 통해 **프로퍼티 값에 접근하면 반환되는 값**이다.
  - 프로퍼티 키를 통해 **프로퍼티 값을 변경**하면 [[Value]] 에 값을 **재할당**한다. 이 때 프로퍼티가 없으면 프로퍼티를 동적 생성하고 생성된 프로퍼티 [[Value]]에 값을 저장한다.
-  **[[Writable]]**
  - 프로퍼티 디스크립터 객체의 프로퍼티 : writable
  - 프로퍼티 값의 **변경 가능 여부**를 나타내며 불리언 값을 갖는다.
  - [[Writable]]의 값이 **false**인 경우 해당 프로퍼티의 [[Value]]의 **값을 변경할 수 없는 읽기 전용 프로퍼티**가 된다.
- **[[Enumerable]]**
  - 프로퍼티 디스크립터 객체의 프로퍼티 : enumerable
  - 프로퍼티의 **열거 기능 여부**를 나타내며 불리언 값을 갖는다.
  - [[Enumerable]]의 값이 false인 경우 해당 프로퍼티는 for...in문이나 Object.keys 메서드 등으로 열거할 수 없다.
- **[[Configurable]]**
  - 프로퍼티 디스크립터 객체의 프로퍼티 : configurable
  - 프로퍼티의 **재정의 기능 여부**를 나타내며 **불리언 값**을 갖는다.
  - [[Configurable]]의 값이 false인 경우 **해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값의 변경이 금지**된다. 단, [[Writable]]이 true인 경우 [[Value]]의 변경과  [[Writable]]을 false로 변경하는 것이 허용된다.

---

```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 취득한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```

위의 예제코드에서, Object.getOwnPropertyDescriptor 메서드가 반환한 프로퍼티 디스크립터 객체의 value 프로퍼티 값은 'Lee'이다. 즉, 프로퍼티 어트리뷰터 [[Value]]의 값이 'Lee'이다.

writable, enumerable, configurable 프로퍼티의 값은 모두 true이다. 즉, 프로퍼티 어트리뷰트 [[Writable]], [[Enumerable]], [[Configurable]]의 값이 모두 true이다.



- 프로퍼티가 생성될 때 [[Value]]의 값은 프로퍼티 값으로 초기화되며 [[Writable]], [[Enumerable]], [[Configurable]]의 값은 true로 초기화된다. 이것은 프로퍼티를 동적 추가해도 동일하다. 

```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```

### 16.3.2 접근자 프로퍼티

- 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

#### 접근자 프로퍼티의 프로퍼티 어트리뷰트

- [[Get]]
  - 프로퍼티 디스크립터 객체의 프로퍼티 : get
  - 접근자 프로퍼티를 통해 **데이터 프로퍼티의 값을 읽을 때** 호출되는 접근자 함수다.
  - 즉, 접근자 프로퍼티 키로 프로퍼티 값에 접근하면 프로퍼티 어트리뷰트 [[Get]]의 값, 즉 getter 함수가 호출되고 그 결과가 프로퍼티 값으로 반환된다.
- [[Set]]
  - 프로퍼티 디스크립터 객체의 프로퍼티 : set
  - 접근자 프로퍼티를 통해 **데이터 프로퍼티의 값을 저장할 때** 호출되는 접근자 함수다.
  - 즉, 접근자 프로퍼티 키로 프로퍼티 값을 저장하면 프로퍼티 어트리뷰트 [[Set]]의 값, 즉 setter 함수가 호출되고 그 결과가 프로퍼티 값으로 저장된다.
- [[Enumerable]]
  - 프로퍼티 디스크립터 객체의 프로퍼티 : enumeragble
  - 데이터 프로퍼티의 [[Enumerable]]과 같다.
- [[Configurable]]
  - 프로퍼티 디스크립터 객체의 프로퍼티 : configurable
  - 데이터 프로퍼티의 [[Configurable]]과 같다.

---

- 접근자 함수는 getter/setter함수라고도 부른다. 

- 접근자 프로퍼티는 getter와 setter함수를 모두 정의할 수도 있고 하나만 정의할 수도 있다. 

```javascript
const person = {
  // 데이터 프로퍼티
  firstName: 'Ungmo',
  lastName: 'Lee',

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set fullName(name) {
    // 배열 디스트럭처링 할당: "31.1 배열 디스트럭처링 할당" 참고
    [this.firstName, this.lastName] = name.split(' ');
  }
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + ' ' + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

// firstName은 데이터 프로퍼티다.
// 데이터 프로퍼티는 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// {value: "Heegun", writable: true, enumerable: true, configurable: true}

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 [[Get]], [[Set]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```

- 위의 예제코드에서, 데이터 프로퍼티는 person 객체의 firstName과 lastName.
- 접근자 프로퍼티는 fullName이며, getter와 setter함수를 가진다.
- 내부 슬록/메서드 관점에서 살펴보자. 접근자 프로퍼티 fullName으로 프로퍼티 값에 접근하면 내부적으로 [[Get]] 내부 메서드가 호출되어 다음과 같이 동작한다.
  - 1. **프로퍼티 키**가 유효한지 확인한다. 프로퍼티 키는 **문자열 또는 심벌**이어야한다. 프로퍼티 키 "fullName"은 문자열이므로 유효한 프로퍼티 키다.
    2. **프로토타입 체인**에서 프로퍼티를 검색한다. person객체에 fullName 프로퍼티가 존재한다.
    3. 검색된 fullName 프로퍼티가 데이터 프로퍼티인지 접근자 프로퍼티인지 확인한다. 접근자 프로퍼티임.
    4. 접근자 프로퍼티  fullName의 프로퍼티 어트리뷰트 [[Get]]의 값, 즉 getter함수를 호출하여 그 결과를 반환한다. 프로퍼티 fullName의 프로퍼티 어트리뷰터 [[Get]]의 값은 Object.getOwnPropertyDescriptor 메서드가 반환하는 프로퍼티 디스크립터 객체의 get 프로퍼티 값과 같다.
- 프로토타입 : 어떤 객체의 상위(뿌모 객체의 역할을 하는 객체. -> 19장

- 접근자 프로퍼티와 데이터 프로퍼티를 구별하는 법

  ```javascript
  // 일반 객체의 __proto__는 접근자 프로퍼티다.
  Object.getOwnPropertyDescriptor(Object.prototype, '__proto__');
  // {get: ƒ, set: ƒ, enumerable: false, configurable: true}
  
  // 함수 객체의 prototype은 데이터 프로퍼티다.
  Object.getOwnPropertyDescriptor(function() {}, 'prototype');
  // {value: {...}, writable: true, enumerable: false, configurable: false}
  ```

  getOwnPropertyDescriptor 메서드가 반환한 값을 보면 된다. 프로퍼티 어트리뷰트를 객체로 표현한 프로퍼티 디스크립터 객체의 프로퍼티가 다르다.

## 16.4 프로퍼티 정의

- 프로퍼티 정의 : 새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의하는 것을 말한다. 

- 예시) 프로퍼티 값을 갱신 가능/열거 가능/ 재정의 가능하도록 할 것인지 정의할 수 있다.

- #### **Object.defineProperty** 

  - 이 메서드를 사용하면 프로퍼티의 어트리뷰트를 정의할 수 있다. 인수로는 객체의 참조와 데이터 프로퍼티의 키인 문자열, 프로퍼티 디스크립터 객체를 전달한다. 

```javascript
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, 'firstName', {
  value: 'Ungmo',
  writable: true,
  enumerable: true,
  configurable: true
});

Object.defineProperty(person, 'lastName', {
  value: 'Lee'
});

let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log('firstName', descriptor);
// firstName {value: "Ungmo", writable: true, enumerable: true, configurable: true}

// 디스크립터 객체의 프로퍼티를 누락시키면 undefined, false가 기본값이다.
descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// [[Enumerable]]의 값이 false인 경우
// 해당 프로퍼티는 for...in 문이나 Object.keys 등으로 열거할 수 없다.
// lastName 프로퍼티는 [[Enumerable]]의 값이 false이므로 열거되지 않는다.
console.log(Object.keys(person)); // ["firstName"]

// [[Writable]]의 값이 false인 경우 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없다.
// lastName 프로퍼티는 [[Writable]]의 값이 false이므로 값을 변경할 수 없다.
// 이때 값을 변경하면 에러는 발생하지 않고 무시된다.
person.lastName = 'Kim';

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 삭제할 수 없다.
// lastName 프로퍼티는 [[Configurable]]의 값이 false이므로 삭제할 수 없다.
// 이때 프로퍼티를 삭제하면 에러는 발생하지 않고 무시된다.
delete person.lastName;

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 재정의할 수 없다.
// Object.defineProperty(person, 'lastName', { enumerable: true });
// Uncaught TypeError: Cannot redefine property: lastName

descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// 접근자 프로퍼티 정의
Object.defineProperty(person, 'fullName', {
  // getter 함수
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set(name) {
    [this.firstName, this.lastName] = name.split(' ');
  },
  enumerable: true,
  configurable: true
});

descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log('fullName', descriptor);
// fullName {get: ƒ, set: ƒ, enumerable: true, configurable: true}

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

- 프로퍼티 디스크립터 객체에서 생략된 어트리뷰트의 기본값

| 프로퍼티 디스크립터 객체의 프로퍼티 | 대응하는 프로퍼티 어트리뷰트 | 생략했을 때의 기본값 |
| ----------------------------------- | ---------------------------- | -------------------- |
| value                               | [[Value]]                    | undefined            |
| get                                 | [[Get]]                      | undefined            |
| set                                 | [[Set]]                      | undefined            |
| writable                            | [[Writable]]                 | false                |
| enumerable                          | [[Enumerable]]               | false                |
| configurable                        | [[Configurable]]             | false                |



#### **Object.defineProperties** 

- 이 메서드를 사용하면 여러개의 프로퍼티를 한 번에 정의할 수 있다.

```javascript
const person = {};

Object.defineProperties(person, {
  // 데이터 프로퍼티 정의
  firstName: {
    value: 'Ungmo',
    writable: true,
    enumerable: true,
    configurable: true
  },
  lastName: {
    value: 'Lee',
    writable: true,
    enumerable: true,
    configurable: true
  },
  // 접근자 프로퍼티 정의
  fullName: {
    // getter 함수
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set(name) {
      [this.firstName, this.lastName] = name.split(' ');
    },
    enumerable: true,
    configurable: true
  }
});

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```



## 16.3 객체 변경 방지

- 객체는 변경 가능한 값이므로 재할당 없이 직접 변겅할 수 있다.

- 즉, 프로퍼티를 추가/삭제할 수 있고, 프로퍼티 값을 갱신할 수 있으며, Object.defineProperty/Object.defineProperties 메서드로 프로퍼티 어트리뷰트를 재정의할 수 있다.

- #### 객체의 변경을 방지하는 메서드

| 구분           | 메서드                   | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
| -------------- | ------------------------ | ------------- | ------------- | ---------------- | ---------------- | -------------------------- |
| 객체 확장 금지 | Object.preventExtensions | X             | O             | O                | O                | O                          |
| 객체 밀봉      | Object.seal              | X             | X             | O                | O                | X                          |
| 객체 동결      | Object.freeze            | X             | X             | O                | X                | X                          |

### 16.5.1 객체 확장 금지

- 객체 확장 금지란, 프로퍼티 추가 금지를 의미한다. 

- #### Object.preventExtensions 

  - 이 메서드는 객체의 확장을 금지한다. 즉, 확장이 금지된 객체는 프로퍼티 추가가 금지된다.
  - 프로퍼티는 프로퍼티 동적 추가와 Object.defineProperty 메서드로 추가할 수 있는데, 이 두 가지 방법이 모두 금지된다.

- 확장이 가능한 객체인지 여부는 Object.isExtensible 메서드로 확인 가능

  ```javascript
  const person = { name: 'Lee' };
  
  // person 객체는 확장이 금지된 객체가 아니다.
  console.log(Object.isExtensible(person)); // true
  
  // person 객체의 확장을 금지하여 프로퍼티 추가를 금지한다.
  Object.preventExtensions(person);
  
  // person 객체는 확장이 금지된 객체다.
  console.log(Object.isExtensible(person)); // false
  
  // 프로퍼티 추가가 금지된다.
  person.age = 20; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 추가는 금지되지만 삭제는 가능하다.
  delete person.name;
  console.log(person); // {}
  
  // 프로퍼티 정의에 의한 프로퍼티 추가도 금지된다.
  Object.defineProperty(person, 'age', { value: 20 });
  // TypeError: Cannot define property age, object is not extensible
  ```

  

### 16.5.2 객체 밀봉

- 객체 밀봉 : 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지를 의미한다.

- #### Object.seal

  - 이 메서드는 객체를 밀봉한다. 즉, **밀봉된 객체는 읽기와 쓰기만 가능**하다.

- 밀봉된 객체인지 여부는 Object.isSealed메서드로 확인 가능

  ```javascript
  const person = { name: 'Lee' };
  
  // person 객체는 밀봉(seal)된 객체가 아니다.
  console.log(Object.isSealed(person)); // false
  
  // person 객체를 밀봉(seal)하여 프로퍼티 추가, 삭제, 재정의를 금지한다.
  Object.seal(person);
  
  // person 객체는 밀봉(seal)된 객체다.
  console.log(Object.isSealed(person)); // true
  
  // 밀봉(seal)된 객체는 configurable이 false다.
  console.log(Object.getOwnPropertyDescriptors(person));
  /*
  {
    name: {value: "Lee", writable: true, enumerable: true, configurable: false},
  }
  */
  
  // 프로퍼티 추가가 금지된다.
  person.age = 20; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 삭제가 금지된다.
  delete person.name; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 값 갱신은 가능하다.
  person.name = 'Kim';
  console.log(person); // {name: "Kim"}
  
  // 프로퍼티 어트리뷰트 재정의가 금지된다.
  Object.defineProperty(person, 'name', { configurable: true });
  // TypeError: Cannot redefine property: name
  ```

  

### 16.5.3 객체 동결

- 객체 동결 : 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지, 프로퍼티 값 갱신 금지를 의미한다.

- #### Object.freeze 

  -  이 메서드는 객체를 동결한다. 즉, **동결된 객체는 읽기만 가능**하다.

- 동결된 객체인지 여부는 Object.isFrozen 메서드로 확인 가능

  ```javascript
  const person = { name: 'Lee' };
  
  // person 객체는 동결(freeze)된 객체가 아니다.
  console.log(Object.isFrozen(person)); // false
  
  // person 객체를 동결(freeze)하여 프로퍼티 추가, 삭제, 재정의, 쓰기를 금지한다.
  Object.freeze(person);
  
  // person 객체는 동결(freeze)된 객체다.
  console.log(Object.isFrozen(person)); // true
  
  // 동결(freeze)된 객체는 writable과 configurable이 false다.
  console.log(Object.getOwnPropertyDescriptors(person));
  /*
  {
    name: {value: "Lee", writable: false, enumerable: true, configurable: false},
  }
  */
  
  // 프로퍼티 추가가 금지된다.
  person.age = 20; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 삭제가 금지된다.
  delete person.name; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 값 갱신이 금지된다.
  person.name = 'Kim'; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 어트리뷰트 재정의가 금지된다.
  Object.defineProperty(person, 'name', { configurable: true });
  // TypeError: Cannot redefine property: name
  ```

  

## 16.5.4 불변 객체

- 객체 확장 금지, 객체 밀봉, 객체 동결 메서드들은 **얕은 변경 방지**(shallow only)로 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 주지는 못한다.

- 예시) Object.freeze메서드로 객체를 동결하여도 중첩 객체까지 동결할 수 없다.

  ```javascript
  const person = {
    name: 'Lee',
    address: { city: 'Seoul' }
  };
  
  // 얕은 객체 동결
  Object.freeze(person);
  
  // 직속 프로퍼티만 동결한다.
  console.log(Object.isFrozen(person)); // true
  // 중첩 객체까지 동결하지 못한다.
  console.log(Object.isFrozen(person.address)); // false
  
  person.address.city = 'Busan';
  console.log(person); // {name: "Lee", address: {city: "Busan"}}
  ```

  

- 객체의 중첩 객체까지 동결하여 변경이 불가능한 읽기 전용의 불변 객체를 구현하려면 객체를 값으로 갖는 **모든 프로퍼티에 대해 재귀적으로** Object.freeze 메서드를 호출해야한다. 

  ```javascript
  function deepFreeze(target) {
    // 객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
    if (target && typeof target === 'object' && !Object.isFrozen(target)) {
      Object.freeze(target);
      /*
        모든 프로퍼티를 순회하며 재귀적으로 동결한다.
        Object.keys 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.
        ("19.15.2. Object.keys/values/entries 메서드" 참고)
        forEach 메서드는 배열을 순회하며 배열의 각 요소에 대하여 콜백 함수를 실행한다.
        ("27.9.2. Array.prototype.forEach" 참고)
      */
      Object.keys(target).forEach(key => deepFreeze(target[key]));
    }
    return target;
  }
  
  const person = {
    name: 'Lee',
    address: { city: 'Seoul' }
  };
  
  // 깊은 객체 동결
  deepFreeze(person);
  
  console.log(Object.isFrozen(person)); // true
  // 중첩 객체까지 동결한다.
  console.log(Object.isFrozen(person.address)); // true
  
  person.address.city = 'Busan';
  console.log(person); // {name: "Lee", address: {city: "Seoul"}}
  ```

  
