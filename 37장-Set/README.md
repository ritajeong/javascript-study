# 37장 Set과 Map

메서드 요약

| 이름                        | 기능           | 비고                                |
| --------------------------- | -------------- | ----------------------------------- |
| Set.prototype.size 프로퍼티 | 요소 개수 확인 | getter함수만 존재                   |
| Set.prototype.add           | 요소 추가      | method chaining 가능                |
| Set.prototype.has           | 요소 존재 확인 | 반환값: boolean                     |
| Set .prototoype.delete      | 요소 삭제      | 반환값: boolean(method chaing 불가) |
| Set.prototype.clear         | 요소 일괄 삭제 | 반환값: undefined                   |
| Set.prototype.forEach       | 요소 순회      |                                     |



## 37.1 Set

Set 객체 : 중복되지 않는 유일한 값들의 집합

수학적 집학을 구현하기 위한 자료구조이며, 그 특성과 일치한다. 따라서 교집합, 합집합, 차집합, 여집합 등을 구현할 수 있다.



배열과 유사하지만 다음에 기술된 항목에 대해서는 **Set은 해당되지 않는다.**

- 동일한 값을 중복하여 포함할 수 있다.
- 요소 순서에 의미가 있다.
- 인덱스로 요소에 접근할 수 있다.



### 37.1.1 Set 객체의 생성

- Set 객체는 Set 생성자 함수로 생성한다. 

```javascript
const set = new Set();
console.log(set); // Set(0){}
```



```javascript
//1. Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다. 
const set1 = new Set([1, 2, 3, 3]);
console.log(set1); // Set(3) {1, 2, 3}


const set2 = new Set('hello');
console.log(set2); // Set(4) {"h", "e", "l", "o"}
//2. 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다.


// 3. 중복을 허용하지 않는 Set 객체의 특성을 활용하여 배열에서 중복된 요소를 제거할 수 있다.
// 배열의 중복 요소 제거
const uniq = array => array.filter((v, i, self) => self.indexOf(v) === i);
console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]

// Set을 사용한 배열의 중복 요소 제거
const uniq = array => [...new Set(array)];
console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
```



### 37.1.2 요소 개수 확인

- Set.prototype.size 프로퍼티를 사용

```javascript
const { size } = new Set([1, 2, 3, 3]);
console.log(size); // 3
```

size 프로퍼티는 setter 함수 없이 getter함수만 존재하는 접근자 프로퍼티.

즉, size 프로퍼티에 숫자를 할당하여 Set 객체의 요소 개수를 변경할 수 없다.

```javascript
const set = new Set([1, 2, 3]);

console.log(Object.getOwnPropertyDescriptor(Set.prototype, 'size'));
// {set: undefined, enumerable: false, configurable: true, get: ƒ}

set.size = 10; // 무시된다.
console.log(set.size); // 3
```

### 37.1.3 요소 추가

- Set.prototype.add 메서드를 사용

```javascript
const set = new Set();
console.log(set); // Set(0) {}

// 1. Set객체에 요소 추가 : add 메서드 사용
set.add(1);
console.log(set); // Set(1) {1}

// 2. method chaining
set.add(2).add(3);
console.log(set); // Set(3) {1, 2, 3}

// 3. 중복된 요소를 추가할 경우 무시됨
set.add(3).add(3);
console.log(set); //Set(3) {1, 2, 3}
```

```javascript

// 4. 일치 비교 연산자를 사용하면 NaN과 NaN은 다르다고 평가됨. 그러나 Set 객체는 이를 같다고 평가하여 중복 추가를 허용하지 않음. +0과 -0도 같은 맥락으로 허용하지 않음.
const set = new Set();

console.log(NaN === NaN); // false
console.log(0 === -0); // true

// NaN과 NaN을 같다고 평가하여 중복 추가를 허용하지 않는다.
set.add(NaN).add(NaN);
console.log(set); // Set(1) {NaN}

// +0과 -0을 같다고 평가하여 중복 추가를 허용하지 않는다.
set.add(0).add(-0);
console.log(set); // Set(2) {NaN, 0}
```

```javascript

// 5. Set 객체는 객체나 배열과 같이 자바스크립트의 모든 값을 요소로 저장가능
const set = new Set();

set
  .add(1)
  .add('a')
  .add(true)
  .add(undefined)
  .add(null)
  .add({})
  .add([]);

console.log(set); // Set(7) {1, "a", true, undefined, null, {}, []}
```

### 37.1.4 요소 존재 여부 확인

- Set.prototype.has 메서드 사용
- 반환값 : 불리언 값. 요소의 존재 여부를 나타냄.

```javascript
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

### 37.1.5 요소 삭제

- Set .prototoype.delete 메서드 사용
- 반환값 : 불리언 값. 삭제 성공 여부.
- 인수 : 삭제하려는 요소값을 전달

```javascript
const set = new Set([1, 2, 3]);

// 요소 2를 삭제한다.
set.delete(2);
console.log(set); // Set(2) {1, 3}

// 요소 1을 삭제한다.
set.delete(1);
console.log(set); // Set(1) {3}

// 존재하지 않는 값에 대한 삭제는 무시됨
set.delete(0);
console.log(set); // Set(1) {3}


// delete는 불리언 값을 반환하므로 method chaining이 불가능
set.delete(1).delete(2); // TypeError: set.delete(...).delete is not a function
```

### 37.1.6 요소 일괄 삭제

- Set.prototype.clear 메서드 사용
- 반환값 : undefined

```javascript
const set = new Set([1, 2, 3]);

set.clear();
console.log(set); // Set(0) {}
```

### 37.1.7 요소 순회

- Set.prototype.forEach 메서드 사용
- 인수 : 콜백함수와 forEach메서드의 콜백함수내부에서 this로 사용될 객체(옵션)를 전달
- 콜백함수의 인수 : 1-현재 순회중인 요소값, 2-현재 순회중인 요소값, 3-현재 순회중인 Set 객체 지체. (첫번째, 두번째 인수가 동일한 이유는 단지 Array.prototype.forEach메서드와 인터페이스를 통일하기 위함.)

```javascript
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));
/*
1 1 Set(3) {1, 2, 3}
2 2 Set(3) {1, 2, 3}
3 3 Set(3) {1, 2, 3}
*/
```

- Set 객체는 이터러블. 따라서 하기의 동작이 가능함
  - for...of문으로 순회 가능. 
  - 스프레드 문법
  - 배열 디스트럭쳐링 

```javascript
const set = new Set([1, 2, 3]);

// Set 객체는 Set.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in set); // true

// 이터러블인 Set 객체는 for...of 문으로 순회할 수 있다.
for (const value of set) {
  console.log(value); // 1 2 3
}

// 이터러블인 Set 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...set]); // [1, 2, 3]

// 이터러블인 Set 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, ...rest] = [...set];
console.log(a, rest); // 1, [2, 3]
```

- Set 객체는 요소의 순서에 의미를 갖지 않지만, Set 객체를 순회하는 순서는 요소가 추가된 순서를 따른다.

### 37.1.8 집합연산

- 교집합

  ```javascript
  // 방법1
  Set.prototype.intersection = function (set) {
    const result = new Set();
  
    for (const value of set) {
      // 2개의 set의 요소가 공통되는 요소이면 교집합의 대상이다.
      if (this.has(value)) result.add(value);
    }
  
    return result;
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA와 setB의 교집합
  console.log(setA.intersection(setB)); // Set(2) {2, 4}
  // setB와 setA의 교집합
  console.log(setB.intersection(setA)); // Set(2) {2, 4}
  
  //방법2
  Set.prototype.intersection = function (set) {
    return new Set([...this].filter(v => set.has(v)));
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA와 setB의 교집합
  console.log(setA.intersection(setB)); // Set(2) {2, 4}
  // setB와 setA의 교집합
  console.log(setB.intersection(setA)); // Set(2) {2, 4}
  ```

- 합집합

  ```javascript
  //방법1
  Set.prototype.union = function (set) {
    // this(Set 객체)를 복사
    const result = new Set(this);
  
    for (const value of set) {
      // 합집합은 2개의 Set 객체의 모든 요소로 구성된 집합이다. 중복된 요소는 포함되지 않는다.
      result.add(value);
    }
  
    return result;
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA와 setB의 합집합
  console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
  // setB와 setA의 합집합
  console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
  
  //방법2
  Set.prototype.union = function (set) {
    return new Set([...this, ...set]);
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA와 setB의 합집합
  console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
  // setB와 setA의 합집합
  console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
  ```

- 차집합

  ```javascript
  //방법1
  Set.prototype.difference = funct\ion (set) {
    // this(Set 객체)를 복사
    const result = new Set(this);
  
    for (const value of set) {
      // 차집합은 어느 한쪽 집합에는 존재하지만 다른 한쪽 집합에는 존재하지 않는 요소로 구성된 집합이다.
      result.delete(value);
    }
  
    return result;
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA에 대한 setB의 차집합
  console.log(setA.difference(setB)); // Set(2) {1, 3}
  // setB에 대한 setA의 차집합
  console.log(setB.difference(setA)); // Set(0) {}
  
  //방법2
  Set.prototype.difference = function (set) {
    return new Set([...this].filter(v => !set.has(v)));
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA에 대한 setB의 차집합
  console.log(setA.difference(setB)); // Set(2) {1, 3}
  // setB에 대한 setA의 차집합
  console.log(setB.difference(setA)); // Set(0) {}
  ```

- 부분집합과 상위집합

  ```javascript
  //방법1
  // this가 subset의 상위 집합인지 확인한다.
  Set.prototype.isSuperset = function (subset) {
    for (const value of subset) {
      // superset의 모든 요소가 subset의 모든 요소를 포함하는지 확인
      if (!this.has(value)) return false;
    }
  
    return true;
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA가 setB의 상위 집합인지 확인한다.
  console.log(setA.isSuperset(setB)); // true
  // setB가 setA의 상위 집합인지 확인한다.
  console.log(setB.isSuperset(setA)); // false
  
  //방법2
  // this가 subset의 상위 집합인지 확인한다.
  Set.prototype.isSuperset = function (subset) {
    const supersetArr = [...this];
    return [...subset].every(v => supersetArr.includes(v));
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);
  
  // setA가 setB의 상위 집합인지 확인한다.
  console.log(setA.isSuperset(setB)); // true
  // setB가 setA의 상위 집합인지 확인한다.
  console.log(setB.isSuperset(setA)); // false
  ```

  

