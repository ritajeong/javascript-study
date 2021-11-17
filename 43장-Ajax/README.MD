# 43장 Ajax

## 43.1 Ajax란?

- Asynchronous JavaScript and XML
- Ajax는 자바스크립트를 사용해 브라우저가 서버와 비동기 방식으로 통식하도록 하는 방법이다.
- Ajax는 XMLHttpRequest 객체 혹은 fetch api를 이용해 구현할 수 있다.
- Ajax를 통해 새로운 HTML 문서를 받지 않아도 화면을 새롭게 갱신할 수 있다.
  - 전통적인 웹 어플리케이션에서는 작은 변경만 있어도 완전한 HTML 문서를 다시 받아야했다.
  - 이때 불 필요한 데이터 통신이 발생할 뿐만 아니라 필연적으로 화면에 깜박거림이 발생한다.

## 43.2 JSON

- JSON(JavaScript Object Notation)은 클라이언트와 서버 간 HTTP 통신을 위한 데이터 포맷
- 처음에는 자바스크립트에서 사용할 목적으로 만들어졌으나 특정 언어에 종속되지 않는 '언어 독립형' 데이터 포맷이므로 많은 프로그래밍 언어에서 사용될 수 있다.
- 자바스크립트의 객체와 유사하게 표현한다. 다만 키와 문자열은 반드시 큰따옴표를 사용해 표현해야한다.

<br>

### JSON의 메서드

1. JSON.stringify(value[, replacer, space])
  - 자바스크립트의 값을 JSON 포맷의 문자열로 변환한다.
  - 위와 같은 과정을 직렬화(serializing)이라 한다.
  ```js
  const obj = {
    name: "Tanney",
    age: 27,
    alive: true,
    hobby: [],
  };

  const json = JSON.stringify(obj);

  console.log(typeof json, json);
  // string {"name": "Tanney", "age": 27, "alive": true, "hobby": []}
  ```
  - JSON은 특정 언어에 종속되지 않는 포맷이므로 자바스크립트 고유의 객체는 처리할 수 없다.
  - 함수 프로퍼티(메서드), 심볼이 키인 프로퍼티, 값이 undefined인 프로퍼티는 무시된다.
  ```js
  const user = {
    sayHi() { // 무시
      alert("Hello");
    },
    [Symbol("id")]: 123, // 무시
    something: undefined // 무시
  };

  alert( JSON.stringify(user) ); // {} (빈 객체가 출력됨)
  ```
  - 순환 참조가 있을 경우 에러가 발생한다.
  - 인자 종류(value, replacer, space)
    - value: 인코딩하려는 값
    - replacer: JSON으로 인코딩 하길 원하는 프로퍼티를 배열 또는 매핑함수로 결정
      - 매핑함수의 경우 프로퍼티 (키, 값) 쌍 전체를 대상으로 호출된다.
      - 매핑함수는 기존 프로퍼티 값을 대신할 값을 반환해야한다.
      - 매핑함수가 undefined를 반환하면 해당 프로퍼티는 무시된다.
      ```js
      const obj = {
        a: "a",
        b: 1,
        c: [],
      };

      console.log(JSON.stringify(obj, ["a", "b"])) // { "a": "a", "b": 1 }
      console.log(JSON.stringify(obj, (key, value) => {
        if (key === "c") {
          return;
        }

        return value + "!!!";
      })); // { "a": "a!!!", "b": "1!!!" }
      ```
    - space: 서식 변경 목적으로 사용할 공백 문자 수
      ```js
      const obj = {
        a: "a",
        b: 1,
        c: [],
      };

      console.log(JSON.stringify(obj, null, 2)); // JSON 포맷의 문자열로 변환하면서 들여쓰기
      /*
      string {
        "a": "a",
        "b": 1,
        "c": [],
      }
      */
      ```
  - 배열도 JSON 문자열로 변환할 수 있다.

<br>

2. JSON.parse

<br>

- JSON 포맷의 문자열을 객체로 변환
  
<br>

### HTTP 요청

<br>

- 브라우저에서는 form 태그나 a 태그를 통한 HTTP 요청 전송 기능을 제공한다.
- AJAX통신을 위한 방법으로 XMLHttpRequest 객체와 fetch API를 제공한다.
  - XMLHttpRequest의 경우 open => setRequestHeader => send 메서드를 호출해 요청을 전송한다.
  - XMLHttpRequest 사용시 onreadystatechange 핸들러를 통해 HTTP 응답을 처리한다.
  - fetch api의 경우 fetch 함수를 통해 요청을 보내며 인수로 method, headers 등의 정보를 넘긴다.
  - fetch 함수는 http응답을 Promise 객체로 반환한다.