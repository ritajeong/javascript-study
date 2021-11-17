# 39장 DOM

- DOM은 HTML문서의 계층적 구조와 정보 그리고 여러 메서드들로 이루어진 트리 자료주조이다.

## 39.1 노드

<br>

### HTML요소
- HTML 요소(HTML Element)는 HTML 문서를 구성하는 개별적인 요소를 의미한다.
- 즉, HTML 문서에서 볼 수 있는 하나하나의 태그 요소들을 HTML 요소라고 부른다.
- HTML요소는 시작 및 종료 태그, 어트리뷰트, 컨텐츠로 이루어진다.
- 이때, 컨텐츠는 텍스트 또는 다른 HTML 요소가 될 수 있다.

```html
<div class="hey">Hey</div>

 <!-- 위 HTML 요소는 다음과 같이 구성된다. -->
<!-- 시작 태그: div -->
<!-- 어트리뷰트: { 이름: class, value: "hey" } -->
<!-- 컨텐츠: "hey" -->
<!-- 종료 태그: div -->
```

<br>

### 노드 객체
- 렌더링 엔진은 HTML 요소를 파싱해 노드 객체를 생성한다.
- 이때, HTML 요소, 어트리뷰트, 텍스트 컨텐츠 등은 각각 타입에 맞는 노드 객체로 변환된다.
  - 예를들어 요소 노드 객체, 어트리뷰트 노드 객체, 텍스트 노드 객체
- 앞서 이야기했듯 HTML 요소의 컨텐츠는 또 다른 HTML 요소가 될 수 있다. 즉, HTML 문서는 각 HTML요소들의 계층적 구조로 이루어진다.
- 따라서 이를 표현하는 노드 객체 또한 계층적 구조를 가지며 루트 노드를 기준으로 트리가 형성된다.
- 이렇게 생성된 노드 객체의 트리를 DOM이라고 부른다.

<br>

### 노드 객체의 타입

- 노드 객체에는 종류가 있고 각각 종류에 따라 다른 상속 구조를 갖는다.
- 노드 객체는 총 12개의 종류가 있다.
- 대표적인 노드 타입은 다음과 같다.
  1. 문서 노드(document node)
      - 문서노드는 DOM 트리의 최상위에 존재하는 루트 노드이다.
      - 실제로 document객체가 이 문서 노드에 해당한다.
      - document객체는 전역 객체인 window의 document 프로퍼티에 바인딩 되어있다.
      - HTML 문서당 document객체는 유일하다.
  2. 요소 노드(element node)
      - HTML 요소를 가리키는 객체이다.
  3. 어트리뷰트 노드(attribute node)
      - HTML 요소의 어트리뷰트를 나타내는 객체
      - 어트리뷰트 노드는 해당 어트리뷰트가 지정된 HTML 요소의 요소 노드와 연결되어 있다.
  4. 텍스트 노드
      - HTML 요소의 텍스트를 가리키는 객체
      - 텍스트 노드는 항상 트리의 리프 노드이다.
- 위 4가지 말고도 주석을 위한 Comment노드, DOCTYPE을 위한 DocumentType노드, DocumentFragment 노드, 공백 텍스트 노드 등이 존재한다.

<br> 

### 노드 객체의 상속 구조
- DOM을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 DOM API를 사용할 수 있다. 
- 이러한 API는 자바스크립트의 프로토타입에 의해 메서드의 형태로 상속된다.
- 노드 객체의 상속 구조는 다음과 같다.
  <br>
  ![노드객체구조](https://user-images.githubusercontent.com/57767891/139620868-a2dcc469-1b34-40e6-9493-bff3d27200dd.png)
- 모든 노드는 Object, EventTarget, Node 객체를 상속받는다.
  - 이벤트 관련 기능은 EventTarget으로부터, 트리 탐색이나 노드 정보 제공 등의 기능은 Node로부터 상속 받는다.
- 문서노드(document)는 Document, HTMLDocument를, 어트리뷰트 노드는 Attr을 텍스트 노드는 CharacterData를 각각 상속받는다.
- 요소 노드는 Element와 HTMLElement를 상속 받는데 각 태그 종류에 따라 HTMLBodyElement, HTMLDivElement 등을 상속받는다.
  - style프로퍼티와 같이 공통의 정보는 HTMLElement로부터 각 요소의 고유 정보는 각 인터페이스로부터 제공받는다.

<br>

## 39.2 요소 노드 취득

<br>

- HTML요소를 동적으로 조작하기위해 특정 요소 노드를 취득할 수 있다.

<br>

### id를 이용한 요소 노드 취득
- Document.prototype.getElementById를 사용
- 인수로 전달한 문자열을 id 어트리뷰트로 갖는 첫번째 요소를 반환한다.
- 해당하는 요소가 존재하지 않으면 null을 반환한다.
- Document.prototype의 메서드이므로 문서노드인 document 객체를 통해 호출해야한다.
- HTML요소에 id 어트리뷰트를 부여하면 id값과 동일한 이름의 전역변수에 해당 노드 객체가 할당된다.
  - id값에 식별자 규칙을 준수하지 않는 문자열이 들어가는 경우는 해당하지 않는다.
  - 이미 id값과 동일한 이름의 전역 변수가 있어도 해당하지 않음.

<br>

### 태그 이름을 이용한 요소 노드 취득
- Document.prototype(or Element.prototype).getElementsByTagName
- 인수로 전달한 태그 이름을 갖는 모든 요소 노드를 HTMLCollection객체로 반환한다.
- HTMLCollection은 유사배열이면서 이터러블이다.
- 모든 요소 노드를 취득하려면 인수로 '*'를 전달한다.
- Document.prototype.getElementsByTagName은 문서 전체를 탐색, Element.prototype.getElementsByTagName은 특정 엘리먼트의 자손을 탐색한다.
- 해당하는 요소가 없다면 빈 HTMLCollection객체를 반환한다.

<br>

### class를 이용한 요소 노드 취득
- Document.prototype(or Element.prototype).getElementsByClassName
- 인수로 전달한 class 어트리 뷰트를 갖는 모든 요소 노드를 HTMLCollection 객체로 반환한다.
- 이하 성질은 getElementsByTagName과 동일하다.

<br>

### CSS 선택자를 이용한 요소 노드 취득
- Document.prototype(or Element.prototype).querySelector 사용
- 인수로 전달한 CSS 선택자를 만족시키는 요소 노드 중 첫번째를 반환한다.
- 만족하는 노드가 없다면 null을 반환한다.
- 인수로 전달한 CSS 선택자가 문법에 맞지 않는 경우 DOMException에러가 발생한다.

<br>

- Document.prototype(or Element.prototype).querySelectorAll을 사용 할 수도 있다.
- 이때는 인수로 전달한 CSS 선택자를 만족시키는 모든 요소 노드를 NodeList 객체로 반환한다.
- NodeList객체도 HTMLCollection과 마찬가지로 유사배열이면서 이터러블이다.
- 만족하는 요소가 없다면 빈 NodeList를 반환한다.
- 인수로 전달한 CSS 선택자가 문법에 맞지 않는 경우 DOMException에러가 발생한다.

<br>

### 특정 요소 노드를 취득할 수 있는지 확인
- Element.prototype.matches 메서드 사용
- 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인하고 결과를 boolean값으로 반환한다.

<br>

### HTMLCollection과 NodeList
- HTMLCollection과 NodeList 모두 DOM API가 여러개의 결과 값을 반환하기 위해 사용하는 DOM 컬렉션 객체이다.
- HTMLCollection과 NodeList 모두 유사 배열이면서 이터러블이다.
- HTMLCollection은 항상 live 객체로 동작한다.
- NodeList의 경우 대부분 non-live 객체로 동작하지만 childNodes 프로퍼티에 의해 참조되는 NodeList의 경우 live 객체로 동작한다.

<br>

> live 객체
> 특정 DOM 컬렉션이 각 노드 객체의 상태 변화를 실시간으로 반영할 때 이를 live 객체라 부른다.

<br>

### 39.3 노드 탐색

- Node와 Elementsms DOM 트리를 탐색 할 수 있는 접근자 프로퍼티를 제공한다.

#### 1. 자식 노드 탐색
- Node.prototype.childNodes
  - 모든 자식 모드를 NodeList로 반환
  - 텍스트 및 빈 텍스트 노드도 포함한다.
- Element.prototype.children
  - 자식 노드 중 요소 노드만 HTMLCollection에 담아 반환
- Node.prototype.fitstChild
  - 첫 번째 자식 노드 반환
  - 텍스트 노드일 수도 있다.
- Node.prototype.lastChild
  - 마지막 자식 노드 반환
  - 텍스트 노드일 수 있다.
- Element.prototype.firstElementChild
  - 첫 번째 자식 '요소' 노드를 반환
- Element.ptototype.lastElementChild
  - 마지막 자식 '요소' 노드를 반환
- Node.prototype.hasChildNodes()
  - 자식노드의 존재여부를 boolean으로 반환
  - 이때, 텍스트노드도 포함해 조회한다.
- Element.prototype.childElementCount
  - 자식 '요소' 노드의 수를 반환

<br>

#### 2. 부모 노드 탐색
- Node.prototype.parentNode
  - 부모 요소 노드를 반환

#### 3. 형제 노드 탐색
- Node.prototype.previousSibling
  - 부모가 같은 형제 노드중 자신의 이전 형제 노드를 반환
  - 텍스트 노드일 수 있다.
  - 해당하는 노드가 없으면 null을 반환
- Node.prototype.nextSibling
  - 부모가 같은 형제 노드 중에서 자신의 다음 형제 노드를 반환
  - 텍스트 노드일 수 있다.
  - 해당하는 노드가 없으면 null을 반환
- Element.prototype.previousElementSibling
  - 부모가 같은 형제 노드중 자신의 이전 형제 '요소' 노드를 반환
  - 해당하는 노드가 없으면 null을 반환
- Element.prototype.nextElementSibling
  - 부모가 같은 형제 노드중 자신의 다음 형제 '요소' 노드를 반환
  - 해당하는 노드가 없으면 null을 반환

<br>

### 39.4 노드 정보 취득

- Node.prototype.nodeType
  - 노드의 타입을 나타내는 상수를 반환
  - 노드 타입 상수는 Node에 정의 되어있다.
    ex)
      Node.ELEMENT_NODE === 1
      Node.TEXT_NODE === 3
      Node.DOCUMENT === 9
      ...
- Node.prototype.nodeName
  - 노드의 이름을 문자열로 반환
    ex)
      요소 노드 - 대문자 문자열로 태그 이름 반환 ("UL, "LI", ...)
      텍스트 노드 - "#text" 반환
      문서 노드 - "#document" 반환