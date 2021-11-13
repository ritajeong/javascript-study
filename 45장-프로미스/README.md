# 45장 프로미스

- 자바스크립트는 비동기 처리를 위한 하나의 패턴으로 콜백 함수를 사용한다. 하지만 전통적인 콜백 패턴은 콜벡 헬로 인해 가독성이 나쁘고 비동기 처리 중 발생한 에러의 처리가 곤란하며 여러 개의 비동기 처리를 한 번에 처리하는 데도 한계가 있다.
- ES6에서는 비동기 처리를 위한 또 다른 패턴으로 프로미스를 도입했다. 프로미스는 전통적인 콜백 패턴이 가진 단점을 보완하며 비동기 처리 시점을 명확하게 표현할 수 있다.

## 45.1 비동기 처리를 위한 콜백 패턴의 단점

### 45.1.1 콜백 헬

```js
// GET 요청을 위한 비동기 함수
const get = url => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콘솔에 출력한다.
      console.log(JSON.parse(xhr.response));
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

// id가 1인 post를 취득
get('https://jsonplaceholder.typicode.com/posts/1');
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ...",
  "body": "quia et suscipit ..."
}
*/
```

- 위 예제의 get 함수는 비동기 함수다. 비동기 함수를 호출하면 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 즉시 종료된다.
- **즉, 비동기 함수 내부의  비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료된다. 따라서 비동기 함수 내부의 비동기로 동작하는 코드에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.**
- 비동기 함수는 비동기 처리 결과를 외부에 반환할  수 없고, 상위 스코프의 변수에 할당할 수도 없다. 따라서 비동기 함수의 처리 결과에 대한 후속 처리는 비동기 함수 내부에서 수행해야 한다. 이때  비동기 함수를 범용적으로 사용하기 위해 콜백 함수를 전달하는 것이 일반적이다.
- 필요에 따라 비동기 처리가 성공하면 호출될 콜백 함수와 비동기 처리가 실패하면 호출될 콜백 함수를 전달할 수 있다.

```js
// GET 요청을 위한 비동기 함수
const get = (url, successCallback, failureCallback) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콜백 함수에 인수로 전달하면서 호출하여 응답에 대한 후속 처리를 한다.
      successCallback(JSON.parse(xhr.response));
    } else {
      // 에러 정보를 콜백 함수에 인수로 전달하면서 호출하여 에러 처리를 한다.
      failureCallback(xhr.status);
    }
  };
};

// id가 1인 post를 취득
// 서버의 응답에 대한 후속 처리를 위한 콜백 함수를 비동기 함수인 get에 전달해야 한다.
get('https://jsonplaceholder.typicode.com/posts/1', console.log, console.error);
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ...",
  "body": "quia et suscipit ..."
}
*/
```

- 콜백 함수를 통해 비동기 처리 결과에 대한 후속 처리의 후속 처리를 하려고 한다면 함수 호출이 중첩되어 복잡도가 높아지는 현상이 발생하는데 이를 **콜백 헬**이라 한다.

- 콜백 헬은 가독성을 나쁘게 하며 실수를 유발하는 원인이 된다. 다음은 콜백 헬이 발생하는 전형적인 사례다.

```js
get('/step1', a => {
  get(`/step2/${a}`, b => {
    get(`/step3/${b}`, c => {
      get(`/step4/${c}`, d => {
        console.log(d);
      });
    });
  });
});
```

### 45.1.2 에러 처리의 한계

- 비동기 처리를 위한 콜백 패턴의 문제점 중 가장 심각한 것은 에러 처리가 곤란하다는 것이다. 

```js
try {
  setTimeout(() => { throw new Error('Error!'); }, 1000);
} catch (e) {
  // 에러를 캐치하지 못한다
  console.error('캐치한 에러', e);
}
```

- setTimeout 함수는 1초 후에 실행되고 이후 에러를 발생시킨다. 하지만 이 에러는 catch 코드 블록에서 캐치되지 않는다.
- 에러는 호출자 방향으로 전파된다. 즉, 콜 스택의 아래 방향으로 전파된다. 하지만 setTimeout함수의 콜백 함수를 호출한 것은 setTimeout 함수가 아니다. 따라서 setTimeout 함수의 콜백 함수가 발생시킨 에러는 catch 블록에서 캐치되지 않는다.
- 비동기 처리를 위한 콜백 패터은 콜백 헬이나 에러 처리가 곤란하다는 문제가 있다. 이를 극복하기 위해 ES6에서 프로미스가 도입되었다.

## 45.2 프로미스의 생성

- Promise 생성자 함수를 new 함수와 함께 호출하면 Promise 객체를 생성한다. Promise 생성자 함수는 비동기 처리를 수행할 콜백 함수를 인수로 받는다. 이 콜백 함수는 resolve와 reject 함수를 인수로 전달받는다.

```js
// 프로미스 생성
const promise = new Promise((resolve, reject) => {
  // Promise 함수의 콜백 함수 내부에서 비동기 처리를 수행한다.
  if (/* 비동기 처리 성공 */) {
    resolve('result');
  } else { /* 비동기 처리 실패 */
    reject('failure reason');
  }
});
```

- Promise 생성자 함수가 인수로 전달받은 콜백 함수 내부에서 비동기 처리를 수행한다. 이때 비동기 처리가 성공하면 resolve 함수를 호출하고, 비동기 처리가 실패하면 reject 함수를 호출한다.
- 프로미스는 다음과 같이 현재 비동기 처리가 어떻게 진행되고 있는지를 나타내는 상태 정보를 갖는다.

| 상태 정보 | 의미                                  | 상태 변경 조건                   |
| --------- | ------------------------------------- | -------------------------------- |
| pending   | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태 |
| fulfilled | 비동기 처리가 수행된 상태(성공)       | resolve 함수 호출                |
| rejected  | 비동기 처리가 수행된 상태(실패)       | reject 함수 호출                 |

- fulfilled 또는 rejected 상태를 settled 상태라고 한다. settled 상태는 fulfilled 또는 rejected 상태와 상관없이 pending이 아닌 상태로 비동기 처리가 수행된 상태를 말한다.
- 프로미스는 pending 상태에서 settled 상태로 변활 수 있다. 하지만 settled 상태가 되면 더는 다른 상태로 변화할 수 없다.

## 45.3 프로미스의 후속 처리 메서드

- 프로미스의 비동기 처리 상태가 변화하면 이에 따른 후속 처리를 해야 한다. 
- 프로미스의 비동기 처리 상태가 변화하면 후속 처리 메서드에 인수로 전달한 콜백 함수가 선택적으로 호출된다.

### 45.3.1 Promise.prototype.then

- then 메서드는 두 개의 콜백 함수를 인수로 전달 받는다.
  - 첫 번째 콜백 함수는 프로미스가 fulfilled 상태가 되면 호출된다.
  - 두 번째 콜백 함수는 프로미스가 rejected 상태가 되면 호출된다.

```js
// fulfilled
new Promise(resolve => resolve('fulfilled'))
  .then(v => console.log(v), e => console.error(e)); // fulfilled

// rejected
new Promise((_, reject) => reject(new Error('rejected')))
  .then(v => console.log(v), e => console.error(e)); // Error: rejected
```

### 45.3.2 Promise.prototype.catch

- catch 메서드는 한 개의 콜백 함수를 인수로 전달받는다. catch 메서드의 콜백 함수는 프로미스가 rejected 상태인 경우만 호출된다.

```js
// rejected
new Promise((_, reject) => reject(new Error('rejected')))
  .catch(e => console.log(e)); // Error: rejected
```

### 45.3.3 Promise.prototype.finally

- finally 메서드는 한 개의 콜백 함수를 인수로 전달 받는다. finally 메서드의 콜백 함수는 프로미스의 성공, 실패와 상관없이 무조건 한 번 호출된다.

```js
new Promise(() => {})
  .finally(() => console.log('finally')); // finally
```

## 45.4 프로미스의 에러 처리

- 비동기 처리에서 발생하는 에러는 then 메서드의 두 번째 콜백 함수로 처리할 수 있다.

```js
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl).then(
  res => console.log(res),
  err => console.error(err)
); // Error: 404
```

- 또는, catch를 사용해 처리할 수도 있다,

```js
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl)
  .then(res => console.log(res))
  .catch(err => console.error(err)); // Error: 404
```

- then 메서드의 두 번쨰 콜백 함수는 첫 번째 콜백 함수에서 발생한 에러를 캐치하지 못 하고 코드가 복잡해져 가독성이 좋지 않다.

```js
promiseGet('https://jsonplaceholder.typicode.com/todos/1').then(
  res => console.xxx(res),
  err => console.error(err)
); // 두 번째 콜백 함수는 첫 번째 콜백 함수에서 발생한 에러를 캐치하지 못한다.
```

- catch 메서드를 사용하면 then 메서드를 호출한 이후에 발생한 에러 뿐만 아니라 then 메서드 내부에서 발생한 에러까지 모두 캐치할 수 있다.
- 따라서 에러 처리는 catch를 사용하는 것을 권장한다.

```js
promiseGet('https://jsonplaceholder.typicode.com/todos/1')
  .then(res => console.xxx(res))
  .catch(err => console.error(err)); // TypeError: console.xxx is not a function
```

## 45.5 프로미스 체이닝

- 45.1.1절에서 구현한 콜백 헬을 프로미스를 사용해 다음과 같이 구현할 수 있다.

```js
const url = 'https://jsonplaceholder.typicode.com';

// id가 1인 post의 userId를 취득
promiseGet(`${url}/posts/1`)
  // 취득한 post의 userId로 user 정보를 취득
  .then(({ userId }) => promiseGet(`${url}/users/${userId}`))
  .then(userInfo => console.log(userInfo))
  .catch(err => console.error(err));
```

- then, catch, finally 메서드느 언제나 프로미스를 반환하므로 연속적으로 호출할 수 있다.
- 프로미스는 프로미스 체이닝을 통해 비동기 처리 결과를 전달받아 후속 처리를 하므로 비동기 처리를 위한 콜백 패턴에서 발생하던 콜백 헬이 발생하지 않는다. 다만 프로미스도 콜백 패턴을 사용하므로 콜백 함수를 사용하지 않는 것은 아니다.

- 콜백 패턴은 가독성이 좋지 않다. 이 문제는 ES8에서 도입된 async/await를 통해 해결할 수 있다. 

```js
const url = 'https://jsonplaceholder.typicode.com';

(async () => {
  // id가 1인 post의 userId를 취득
  const { userId } = await promiseGet(`${url}/posts/1`);

  // 취득한 post의 userId로 user 정보를 취득
  const userInfo = await promiseGet(`${url}/users/${userId}`);

  console.log(userInfo);
})();
```

## 45.6 프로미스의 정적 메서드

- Promise는 주로 생성자 함수로 사용되지만 함수도 객체이므로 메서드를 가질 수 있다. 
- Promise가 가지는 메서드는 다음과 같다.
  - Promise.resolve / Promise.reject
  - Promise.all
  - Promise.race
  - Promise.allSettled

> 책에 나오지 않지만 ES2021 스펙으로 Promise.any 라는 메서드가 추가되었습니다.
>
> https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/any

## 45.7 마이크로태스크 큐

- 다음 예제를 살펴보고 어떤 순서로 로그가 출력될지 생각해보자.

```js
setTimeout(() => console.log(1), 0);

Promise.resolve()
  .then(() => console.log(2))
  .then(() => console.log(3));
```

- 1 -> 2 -> 3 의 순으로 출력될 것처럼 보이지만 2 -> 3 -> 1의 순으로 출력된다.
- 그 이유는 프로미스의 메서드는 태스크 큐가 아니라 마이크로태스크 큐에 저장되기 때문이다.
- **마이크로 태스크 큐는 태스크 큐보다 우선순위가 높다.**

## 45.8 fetch

- fetch 함수는 XMLHttpRequest 객체와 마찬가지로 HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API다.
- **fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환한다.**

