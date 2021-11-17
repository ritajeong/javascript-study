# 49장 Babel과 Webpack을 이용한 ES6+/ES.NEXT 개발환경 구축

## 49.1 Babel

<br>

- 바벨은 트랜스파일러다.
- 트랜스파일한다는 건 같은 추상화 수준의 언어로 변환하는 것을 의미한다.
- 예를 들어 자바스크립트 최신 문법이 구형 브라우저에서도 동작하도록 하거나 typescript, jsx를 자바스트립트 코드로 변환할 때 사용한다.

<br>

### 바벨 기본 동작
바벨은 다음 세 단계로 동작한다.

<br>

1. 추상구문트리(AST)로 파싱
2. AST를 변환
3. 결과물 코드를 출력

<br>

### 플러그인(Plugin)

- 이때, AST를 변환하는 일은 babel의 plugin이 수행한다.
- babel 플러그인은 'visitor'라는 프로퍼티를 가진 객체를 반환하는 함수이다.
- visitor의 값은 여러 메서드를 객체이다.
- visitor의 메서드를 원하는 동작으로 재정의해 사용한다.
- 커스텀 플러그인을 구현하지 않더라도 babel에서 제공하는 플러그인을 사용할 수 있다.(홈페이지에서 검색 가능)

### 바벨 간단한 사용
- 바벨을 사용하기 위해선 babel-core와 babel-cli 패키지를 설치해야한다.
  ```
  npm i -D @babel/core @babel/cli
  ```
- 그리고 사용하고자 하는 플러그인을 설치한다.
- 이번 예시에서는 const, let 키워드를 var로 바꿔주는 block-scoping 플러그인을 사용하도록 하겠다.
  ```
  npm install -D @babel/plugin-transform-block-scoping
  ```
- 설치한 플러그인은 다음과 같이 사용할 수 있다.
  ```js
  // app.js
  const a = 1;
  ```

  ```
  npx babel app.js --plugins @babel/plugin-transform-block-scoping

  var a = 1;
  ```
- babel.config.json(혹은 .babelrc)파일을 이용하면 사용하고자하는 플러그인을 미리 지정할 수 있다.
  ```json
  // babel.config.json
  {
    "plugins": ["@babel/plugin-transform-block-scoping"]
  }
  ```
- 뿐만아니라 변환시 여러옵션을 줄 수 있는데 이번에는 결과물을 파일로 생성하는 옵션만 알아보도록 하겠다.
  ```
  // -d 또는 --out-dir
  npx babel app.js -d dist/js
  ```
  ```js
  // dist/js/app.js
  var a = 1;
  ```

<br>

### 프리셋(Preset)

<br>

- 사용하는 바벨 플러그인의 양이 많다면 프리셋을 고려해도 좋다.
- 프리셋은 목적에 맞게 여러 플러그인을 모아 놓은 것을 의미한다.
- 프리셋은 기본적으로 다음과 같이 생성한다.
  ```js
  // mypreset.js
  module.exports = function mypreset() {
    return {
      // 원하는 플러그인들을 작성
      plugins: [
        "@babel/plugin-transform-arrow-functions",
        "@babel/plugin-transform-block-scoping",
        "@babel/plugin-transform-strict-mode",
      ],
    }
  }
  ```
- 그리고 babel 설정 파일을 통해 프리셋을 사용하도록 지정할 수 있다.
  ```json
  {
    presets: ["./mypreset.js"]
  }
  ```
- 바벨은 목적에 따라 여러가지 프리셋을 제공한다. 이를 패키지로 설치해 사용할 수 있다.
  - 예를 들어 다음과 같은 프리셋들을 제공한다.
    preset-env, preset-react, preset-typescirpt, ....
- 프리셋은 총 세가지 방식으로 전달할 수 있다.
  ```json
  {
    "presets": [
        "presetA", // bare string
        ["presetA"], // wrapped in array
        ["presetA", {}] // 2nd argument is an empty options object
      ]
  }
  ```
- 이때, 배열로 프리셋을 전달하면 두번째요소에 option객체를 전달할 수 있다.
- 실제로 env 프리셋의 경우 브라우저를 특정하기 위한 target 옵션과 폴리필 지정을 위한 옵션 등을 제공한다.

> 폴리필<br>
> 자바스크립트 명세에는 새로운 문법이나 기존에 없던 내장 함수들이 추가되곤한다.
> 새로운 문법은 트랜스 파일러를 통해 변환할 수 있지만 새로운 함수는 직접 명세에 맞게 정의해주어야한다.
> 이를 지원하기 위해 폴리필을 사용한다. 
> 폴리필은 기존 함수를 수정하거나 새롭게 구현한 함수의 스크립트를 의미한다.
>

<br>

## 49.2 Webpack

<br>

- Webpack은 의존관계에 있는 자바스크립트, CSS, 이미지 등의 리소스를 하나의 파일로 번들링하는 모듈 번들러이다.
- webpack, wepack-cli 패키지를 설치해 사용한다.
- 바벨과 마친가지로 cli 명령어를 통해 사용한다. 이때 여러 옵션을 줄 수 있지만 webpack.config.js 파일을 통해 옵션을 지정하는게 일반적이다.
- webpack 설정 파일에서 기본적으로 3가지 옵션을 설정해야한다.
  ```js
  // webpack.config.js
  const path = require("path")

  module.exports = {
    mode: "development",
    entry: {
      main: "./src/app.js",
    },
    output: {
      filename: "[name].js",
      path: path.resolve("./dist"),
    },
  }
  ```
- mode는 production과 development 중 하나로 지정한다.
- entry는 번들 진입점이 될 파일을 의미한다. 해당 파일을 기준으로 의존성을 따라 번들링한다.
- output은 번들 결과물에 대한 정보를 의미한다. 위 코드에서는 파일 이름과 경로를 지정해주었다.
  - 'name'은 entry에서 지정한 각 파일의 key값을 의미한다.
- 위 설정에 경우 src 폴더 안에 있는 app.js를 기준으로 번들링해 그 결과물을 dist 폴더 안에 app.js 파일로 생성한다.

<br>

### 로더(Loader)

<br>

- 자바스크립트 뿐만 아니라 CSS, 이미지 등도 함께 번들링하려면 반드시 로더가 필요하다.
- 즉, 로더는 다른 언어(예를 들어 typescript)를 자바스크립트로 변환해주거나, 이미지를 dataURL로 바꿔주는 등 모든 리소스를 js 파일 안에서 사용할 수 있도록 바꾼다.
- 로더는 파일의 내용을 받아 변환 내용을 반환하는 함수를 내보내는 모듈이다.
- 로더를 사용하기 위해 rules 옵션을 사용한다. 사용하고자하는 로더 정보 객체를 rules 배열에 추가한다.
- CSS파일을 위한 로더로 예를 들어보자
  ```js
  // npm i -D style-loader css-loader

  module.exports = {
    rules: [
      { 
        test: /\.css$/,
        use: ["style-loader", "css-loader"]
      }
    ]
  }
  ```
- test는 로더를 적용하고자 하는 파일을 지정한다. 파일명이나 정규표현식 패턴을 지정할 수 있다.
- use는 실제로 적용할 로더의 배열을 의미한다.
- use 배열의 로더들은 뒤에서부터 차례로 적용된다.
- css 파일을 번들링 하기 위해서는 반드시 css-loader를 먼저 적용하고 style-loader를 적용해야한다. 따라서 위와 같은 순서를 반드시 지켜야한다.
- 로더를 사용하면 바벨도 함께 적용할 수 있다. 이를 위해 babel-loader를 사용한다.

<br>

### 플러그인

<br>

- 로더가 파일 단위로 '번들링 과정'에서 적용된다면 플러그인은 번들 결과물에 대해 적용된다.
- plugin은 클래스로 구현하며 apply메서드를 작성해야한다.
- 자세한 내용은 [웹팩 문서](https://webpack.js.org/contribute/writing-a-plugin/)를 통해 확인할 수 있다.
- 바벨과 마찬가지로 웹팩 플러그인도 패키지로 설치해 사용할 수 있다.
- 웹팩 설정 파일에 plugins 옵션을 추가해 사용한다.
- plugins 배열에는 각 플러그인의 인스턴스가 담겨야한다.
- 예시
    ```js
    const webpack = require("webpack")

    export default {
      // 웹팩에서 제공하는 define plugin
      plugins: [new webpack.DefinePlugin({})],
    }
    ```