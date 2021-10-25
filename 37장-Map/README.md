# 37장 Set과 Map

메서드 요약

| 이름                        | 기능           | 비고                                                         |
| --------------------------- | -------------- | ------------------------------------------------------------ |
| Map.prototype.size 프로퍼티 | 요소 개수 확인 | getter함수만 존재                                            |
| Map.prototype.add           | 요소 추가      | method chaining 가능                                         |
| Map.prototype.has           | 요소 존재 확인 | 반환값: boolean                                              |
| Map .prototoype.delete      | 요소 삭제      | 반환값: boolean(method chaing 불가)                          |
| Map.prototype.clear         | 요소 일괄 삭제 | 반환값: undefined                                            |
| Map.prototype.forEach       | 요소 순회      |                                                              |
| Map.prototype.keys          |                | 요소키를 값으로 갖는 이터러블이면서 이터레이터인 객체를 반환 |
| Map.prototype.values        |                | 요소값을 값으로 갖는 이터러블이면서 이터레이터인 객체를 반환 |
| Map.prototype.entries       |                | 요소키와 요소값을값으로 갖는 이터러블이면서 이터레이터인 객체를 반환 |



## 37.2  Map

- Map 은 키와 값의 쌍으로 이루어진 컬렉션이다. 

- 객체와 유사하지만 차이점이 있다.

  | 구분                   | 객체                    | Map 객체              |
  | ---------------------- | ----------------------- | --------------------- |
  | 키로 사용할 수 있는 값 | 문자열 또는 심벌 값     | 객체를 포함한 모든 값 |
  | 이터러블               | X                       | O                     |
  | 요소 개수 확이         | Object.keys(obj).length | map.size              |

### 37.2.1 Map 객체의 생성

- Map 생성자 함수로 생성

```javascript
const map = new Map();
console.log(map); // Map(0) {}
```

Map 생성자 함수는 이터러블을 인수로 전달받아 Map객체를 생성함. 이때 인수로 전달되는 이터러블은 키와 값의 쌍으로 이루어진 요소로 구성돼야함

```javascript
const map1 = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(map1); // Map(2) {"key1" => "value1", "key2" => "value2"}

const map2 = new Map([1, 2]); // TypeError: Iterator value 1 is not an entry object
```

Map 생성자 함수의 인수로 전달한 이터러블에 중복된 키를 갖는 요소가 존재하면 갑싱 덮어씌어짐. 즉, Map 객체에는 중복된 키를 갖는 요소가 존재할 수 없다.

```javascript
const map = new Map([['key1', 'value1'], ['key1', 'value2']]);
console.log(map); // Map(1) {"key1" => "value2"}
```



### 37.2.2 요소 개수 확인

- Map.prototype.size 프로퍼티를 사용

  ```javascript
  const { size } = new Map([['key1', 'value1'], ['key2', 'value2']]);
  console.log(size); // 2
  
  ```

  size 프로퍼티는 setter 함수 없이 getter함수만 존재하는 접근자 프로퍼티.

  즉, size 프로퍼티에 숫자를 할당하여 Map 객체의 요소 개수를 변경할 수 없다.

  ```javascript
  const map = new Map([['key1', 'value1'], ['key2', 'value2']]);
  
  console.log(Object.getOwnPropertyDescriptor(Map.prototype, 'size'));
  // {set: undefined, enumerable: false, configurable: true, get: ƒ}
  
  map.size = 10; // 무시된다.
  console.log(map.size); // 2
  ```

### 37.2.3 요소 추가

- Map.prototype.set 메서드 사용

  ```javascript
  const map = new Map();
  console.log(map); // Map(0) {}
  
  map.set('key1', 'value1');
  console.log(map); // Map(1) {"key1" => "value1"}
  ```

- method chaining 가능

  ```javascript
  const map = new Map();
  
  map
    .set('key1', 'value1')
    .set('key2', 'value2');
  
  console.log(map); // Map(2) {"key1" => "value1", "key2" => "value2"}
  ```

- 중복된 키를 갖는 요소가 존재할 수 없으므로, 중복된 키를 갖는 요소를 추가하면 값이 덮어씌워짐. 에러발생X

  ```javascript
  const map = new Map();
  
  map
    .set('key1', 'value1')
    .set('key1', 'value2');
  
  console.log(map); // Map(1) {"key1" => "value2"}
  ```

- 일치 비교 연산자를 사용하면 NaN과 NaN은 다르다고 평가됨. 그러나 Set 객체는 이를 같다고 평가하여 중복 추가를 허용하지 않음. +0과 -0도 같은 맥락으로 허용하지 않음.

  ```javascript
  const map = new Map();
  
  console.log(NaN === NaN); // false
  console.log(0 === -0); // true
  
  // NaN과 NaN을 같다고 평가하여 중복 추가를 허용하지 않는다.
  map.set(NaN, 'value1').set(NaN, 'value2');
  console.log(map); // Map(1) { NaN => 'value2' }
  
  // +0과 -0을 같다고 평가하여 중복 추가를 허용하지 않는다.
  map.set(0, 'value1').set(-0, 'value2');
  console.log(map); // Map(2) { NaN => 'value2', 0 => 'value2' }
  ```

- Map 객체는 키 타입에 제한이 없다. 따라서 객체를 포함한 모든 값을 키로 사용할 수 있다.

  ```javascript
  const map = new Map();
  
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  // 객체도 키로 사용할 수 있다.
  map
    .set(lee, 'developer')
    .set(kim, 'designer');
  
  console.log(map);
  // Map(2) { {name: "Lee"} => "developer", {name: "Kim"} => "designer" }
  ```

  

### 37.2.4 요소 취득

- Map.prototype.get 메서드를 사용

- 인수 : 키를 전달

- 반환 : 인수로전달한 키를 갖는 `값`을 반환. 없다면 undefined를 반환

  ```javascript
  const map = new Map();
  
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  map
    .set(lee, 'developer')
    .set(kim, 'designer');
  
  console.log(map.get(lee)); // developer
  console.log(map.get('key')); // undefined
  ```

### 37.2.5 요소 존재 여부 확인

- Map.prototype.has 메서드

- 반환 : 불리언 값. 특정 요소의 존재 여부를 나타냄

  ```javascript
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  const map = new Map([[lee, 'developer'], [kim, 'designer']]);
  
  console.log(map.has(lee)); // true
  console.log(map.has('key')); // false
  ```

### 37.2.6 요소 삭제

- Map.prototype.delete 메서드 사용

- 반환값 : 불리언 값. 삭제 성공 여부.

- 인수 : 삭제하려는 키를 전달

  ```javascript
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  const map = new Map([[lee, 'developer'], [kim, 'designer']]);
  
  map.delete(kim);
  console.log(map); // Map(1) { {name: "Lee"} => "developer" }
  ```

  존재하지 않는 키를 인수로 전달하면 에러없이 무시됨.

  ```javascript
  const map = new Map([['key1', 'value1']]);
  
  // 존재하지 않는 키 'key2'로 요소를 삭제하려 하면 에러없이 무시된다.
  map.delete('key2');
  console.log(map); // Map(1) {"key1" => "value1"}
  ```

   delete는 불리언 값을 반환하므로 method chaining이 불가능

  ```javascript
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  const map = new Map([[lee, 'developer'], [kim, 'designer']]);
  
  map.delete(lee).delete(kim); // TypeError: map.delete(...).delete is not a function
  ```

### 37.2.7 요소 일괄 삭제

- Map.prototype.clear 메서드 사용

- undefined 반환

  ```javascript
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  const map = new Map([[lee, 'developer'], [kim, 'designer']]);
  
  map.clear();
  console.log(map); // Map(0) {}
  ```

### 37.2.8 요소 순회

- Map.prototype.forEach 메서드 사용

- 인수 : 콜백함수와 forEach메서드의 콜백함수내부에서 this로 사용될 객체(옵션)를 전달

- 콜백함수의 인수 : 1-현재 순회중인 요소값, 2-현재 순회중인 요소키, 3-현재 순회중인 Map객체 지체. 

  ```javascript
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  const map = new Map([[lee, 'developer'], [kim, 'designer']]);
  
  map.forEach((v, k, map) => console.log(v, k, map));
  /*
  developer {name: "Lee"} Map(2) {
    {name: "Lee"} => "developer",
    {name: "Kim"} => "designer"
  }
  designer {name: "Kim"} Map(2) {
    {name: "Lee"} => "developer",
    {name: "Kim"} => "designer"
  }
  */
  ```

- Map 객체는 이터러블. 따라서 하기의 동작이 가능함

  - for...of문으로 순회 가능. 
  - 스프레드 문법
  - 배열 디스트럭쳐링 

  ```javascript
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };
  
  const map = new Map([[lee, 'developer'], [kim, 'designer']]);
  
  // Map 객체는 Map.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
  console.log(Symbol.iterator in map); // true
  
  // 이터러블인 Map 객체는 for...of 문으로 순회할 수 있다.
  for (const entry of map) {
    console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
  }
  
  // 이터러블인 Map 객체는 스프레드 문법의 대상이 될 수 있다.
  console.log([...map]);
  // [[{name: "Lee"}, "developer"], [{name: "Kim"}, "designer"]]
  
  // 이터러블인 Map 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
  const [a, b] = map;
  console.log(a, b); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
  ```

  

### Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드를 제공한다.

- Map.prototype.keys : 요소키를 값으로 갖는 이터러블이면서 이터레이터인 객체를 반환
- Map.prototype.values : 요소값을 값으로 갖는 이터러블이면서 이터레이터인 객체를 반환
- Map.prototype.entries : 요소키와 요소값을값으로 갖는 이터러블이면서 이터레이터인 객체를 반환

```javascript
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

// Map.prototype.keys는 Map 객체에서 요소키를 값으로 갖는 이터레이터를 반환한다.
for (const key of map.keys()) {
  console.log(key); // {name: "Lee"} {name: "Kim"}
}

// Map.prototype.values는 Map 객체에서 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const value of map.values()) {
  console.log(value); // developer designer
}

// Map.prototype.entries는 Map 객체에서 요소키와 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const entry of map.entries()) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}
```

