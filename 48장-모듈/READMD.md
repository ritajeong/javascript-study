# 48장 모듈

## 48.1 모듈의 일반적인 의미

<br>

- 모듈은 앱을 구성하는 개별요소로 재사용할 수 있는 코드 조각을 의미한다.
- 모듈은 자기만의 모듈 스코프를 가져야한다.
- 보통 모듈은 파일 단위로 분리한다.
- 모듈은 선택적으로 자기가 가진 리소스를 공개할 수 있다. (export)
- 모듈의 사용자는 모듈이 공개한 리소스 중 일부 또는 전체를 자신의 스코프 내로 불러들여 사용할 수 있다. (import)

<br>

## 48.2 자바스크립트 모듈

<br>
 
 - 기존 자바스크립트는 모듈 시스템을 가지고 있지 않았다.
 - script 태그로 불러온 여러 스크립트들은 마치 하나의 파일안에 작성된 코드들처럼 동작한다.
 - 자바스크립트에서 모듈시스템을 사용하기 위해 CommonJS와 AMD의 방식이 제안되기도 했고, ES6에서는 ESM 방식이 도입되었다.

 <br>

 ## 48.3 ES6 모듈(ESM)

 <br>

 - 특정 스크립트를 ESM으로 취급하기 위해서는 script태그에 type="module" 어트리뷰트를 추가해야한다.
 - ESM에서는 기본적으로 strict mode가 적용된다.
 - ESM은 독자적인 모듈 스코프를 제공한다.
 - export 키워드를 통해 특정 식별자를 공개할 수 있다.
    ```js
    export const a = 1;
    export function f() {}
    export class C {}

    // 혹은 하나의 객체로 묶어 공개할 수도 있다.
    const a = 1;
    function f() {}
    class C {}

    export { a, f, C };
    ```
 - 모듈안에서 하나의 값에 한해 default export 값을 정할 수 있다.
    ```js
    const a = 1;

    export default a;

    // 혹은 이러한 방식도 가능하다.

    export default x => x + 1;
    ```  
 - default 키워드를 사용하는 경우 const, let, var 키워드는 함께 사용할 수 없다.
 - import 키워드를 통해 다른 모듈이 공개한 값을 가져올 수 있다.
 - 기본적으로 export한 식별자의 이름을 그대로 사용해 import 해야한다.
    ```js
    import { a, f, C } from "./someModule.js"
    ```
 - as 키워드를 통해 식별자 이름은 변경하여 import 할 수 있다.
    ```js
    import { a as A, f as F, C as CC } from "./someModule.js"
    ```
 - '*'를 통해 모든 리소스를 한 번에 import 할 수도 있다. 이때, 반드시 as 키워드를 통해 식별자 명을 지정해줘야한다.
    ```js
    import * as someModule from "./someModule.js"
    ```
 - default export의 경우 {} 없이 임의의 이름으로 import 한다.
    ```js
    // someModule.js
    const a = 1;

    export default a;

    // index.js
    import A from "./someModule.js"
    ```

