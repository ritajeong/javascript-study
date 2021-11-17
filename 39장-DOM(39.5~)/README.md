# 39장 DOM

### 39.5 요소 노드의 텍스트 조작

#### 39.5.1 nodeValue

---

- Node.prototype.nodeValue 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티다.

- 노드 객체의 nodeValue 프로퍼티를 참조하면 노드 객체의 값을 반환한다. 여기서 노드 객체의 값이란 텍스트 노드의 텍스트다.

  ```html
  <html>
    <body>
      <div id="foo">Hello</div>
    </body>
    <script>
      // 문서 노드의 nodeValue 프로퍼티를 참조한다.
      console.log(document.nodeValue); // null
  
      // 요소 노드의 nodeValue 프로퍼티를 참조한다.
      const $foo = document.getElementById('foo');
      console.log($foo.nodeValue); // null
  
      // 텍스트 노드의 nodeValue 프로퍼티를 참조한다.
      const $textNode = $foo.firstChild;
      console.log($textNode.nodeValue); // Hello
    </script>
  </html>
  ```

<br>

#### 39.5.2 textContent

---

- Node.prototype.textContent 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 텍스트와 모든 자손 노드의 텍스트를 모두 취득하거나 변경한다.
- 요소 노드의 textContent 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역 내의 텍스트를 모두 반환한다.

```html
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // #foo 요소 노드는 텍스트 노드가 아니다.
    console.log(document.getElementById('foo').nodeValue); // null
    // #foo 요소 노드의 자식 노드인 텍스트 노드의 값을 취득한다.
    console.log(document.getElementById('foo').firstChild.nodeValue); // Hello
    // span 요소 노드의 자식 노드인 텍스트 노드의 값을 취득한다.
    console.log(document.getElementById('foo').lastChild.firstChild.nodeValue); // world!
  </script>
</html>
```

<br>

### 39.6 DOM 조작

---

- DOM 조작은 새로운 노드를 생성하여 DOM에 추가하거나 기존 노드를 삭제 또는 교체하는 것을 말한다.
- DOM 조작에 의해 DOM에 새로운 노드가 추가되거나 삭제되면 리플로우와 리페인트가 발생하는 원인이 디므로 성능에 영향을 준다.


#### 39.6.1 innerHTML

---

- Element.prototype.innerHTML 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 HTML 마크업을 취득하거나 변경한다.

- 아래처럼 innerHTML 프로퍼티를 사용하면 HTML 마크업 문자열로 간단히 DOM 조작이 가능하다.

  ```html
  <html>
    <body>
      <ul id="fruits">
        <li class="apple">Apple</li>
      </ul>
    </body>
    <script>
      const $fruits = document.getElementById('fruits');
  
      // 노드 추가
      $fruits.innerHTML += '<li class="banana">Banana</li>';
  
      // 노드 교체
      $fruits.innerHTML = '<li class="orange">Orange</li>';
  
      // 노드 삭제
      $fruits.innerHTML = '';
    </script>
  </html>
  ```

- innerHTML 프로퍼티를 사용한 DOM 조작은 구현이 간단하고 직관적이라는 장점이 있지만 크로스 사이트 스크립트 공격에 취약하다는 단점이 있다.

<br>

#### 39.6.2 insertAdjacentHTML 메서드

---

- Element.prototype.insertAdjacentHTML(position, DOMString) 메서드는 기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입한다.

- ```html
  <html>
    <body>
      <!-- beforebegin -->
      <div id="foo">
        <!-- afterbegin -->
        text
        <!-- beforeend -->
      </div>
      <!-- afterend -->
    </body>
    <script>
      const $foo = document.getElementById('foo');
  
      $foo.insertAdjacentHTML('beforebegin', '<p>beforebegin</p>');
      $foo.insertAdjacentHTML('afterbegin', '<p>afterbegin</p>');
      $foo.insertAdjacentHTML('beforeend', '<p>beforeend</p>');
      $foo.insertAdjacentHTML('afterend', '<p>afterend</p>');
    </script>
  </html>
  ```

<br>

#### 39.6.3 노드 생성과 추가

---

- DOM은 노드를 직접 생성/삽입/삭제/치환하는 메서드도 제공한다.

   - 아래의 과정을 통해서 노드의 생성과 추가가 이루어진다. (추후 설명 추가 예정)

          - 요소 노드 생성
          - 텍스트 노드 생성
          - 텍스트 노드를 요소 노드의 자식 노드로 추가
          - 요소 노드를 DOM에 추가

     ```html
     <html>
       <body>
         <ul id="fruits">
           <li>Apple</li>
         </ul>
       </body>
       <script>
         const $fruits = document.getElementById('fruits');
     
         // 1. 요소 노드 생성
         const $li = document.createElement('li');
     
         // 2. 텍스트 노드 생성
         const textNode = document.createTextNode('Banana');
     
         // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
         $li.appendChild(textNode);
     
         // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
         $fruits.appendChild($li);
       </script>
     </html>	
     ```

<br>

#### 39.6.4 복수의 노드 생성과 추가

---

- DOM을 여러번 변경하는 것은 각각 리플로우와 리페인팅을 야기시킨다. 이를 피하기 위해 컨테이너 요소를 사용할 수 있다.

  ```html
  <html>
    <body>
      <ul id="fruits"></ul>
    </body>
    <script>
      const $fruits = document.getElementById('fruits');
  
      // DocumentFragment 노드 생성
      const $fragment = document.createDocumentFragment();
  
      ['Apple', 'Banana', 'Orange'].forEach(text => {
        // 1. 요소 노드 생성
        const $li = document.createElement('li');
  
        // 2. 텍스트 노드 생성
        const textNode = document.createTextNode(text);
  
        // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
        $li.appendChild(textNode);
  
        // 4. $li 요소 노드를 DocumentFragment 노드의 마지막 자식 노드로 추가
        $fragment.appendChild($li);
      });
  
      // 5. DocumentFragment 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
      $fruits.appendChild($fragment);
    </script>
  </html>
  ```

- 먼저 DocumentFragment 노드를 생성하고 DOM에 추가할 요소 노드를 생성하여 DocumentFragment 노드에 자식 노드로 추가한 다음, DocumentFragment 노드를 기존 DOM에 추가한다.
  - 이때, 실제로 DOM 변경이 발생하는 것은 한 번뿐이며 리플로우와 리페인트도 한 번만 실행한다.
  - 따라서, 여러 개의 요소 노드를 DOM에 추가하는 경우 DocumentFragment 노드를 사용하는 것이 더 효율적이다.

<br>

#### 39.6.5 노드 삽입

---

**마지막 노드로 추가**

- Node.prototype.appendChild 메서드는 인수로 전달받은 노드를 자신을 호출한 노드의 마지막 자식 노드로 DOM에 추가한다.
  - 단, 노드를 추가할 위치를 지정할 수 없고 언제나 마지막 자식 노드로 추가한다.

```html
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    document.getElementById('fruits').appendChild($li);
  </script>
</html>
```



<br>

**지정한 위치에 노드 삽입**

- Node.prototype.insertBefore(newNode, childNode) 메서드는 첫 번째 인수로 전달받은 노드를 두 번째 인수로 전달받은 노드 앞에 삽입한다.

```html
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 요소 앞에 삽입
    $fruits.insertBefore($li, $fruits.lastElementChild);
    // Apple - Orange - Banana
  </script>
</html>
```

<br>

#### 39.6.6 노드 이동

---

- DOM에 이미 존재하는 노드를 appendChild 또는 insertBefore 메서드를 사용하여 DOM에 다시 추가하면 현재 위치에서 노드를 제거하고 새로운 위치에 노드를 추가한다. 즉, 노드가 이동한다.

  ```html
  <html>
    <body>
      <ul id="fruits">
        <li>Apple</li>
        <li>Banana</li>
        <li>Orange</li>
      </ul>
    </body>
    <script>
      const $fruits = document.getElementById('fruits');
  
      // 이미 존재하는 요소 노드를 취득
      const [$apple, $banana, ] = $fruits.children;
  
      // 이미 존재하는 $apple 요소 노드를 #fruits 요소 노드의 마지막 노드로 이동
      $fruits.appendChild($apple); // Banana - Orange - Apple
  
      // 이미 존재하는 $banana 요소 노드를 #fruits 요소의 마지막 자식 노드 앞으로 이동
      $fruits.insertBefore($banana, $fruits.lastElementChild);
      // Orange - Banana - Apple
    </script>
  </html>
  ```

<br>

#### 39.6.7 노드 복사

---

- Node.prototype.cloneNode([deep: true | false]) 메서드는 노드의 사본을 생성하여 반환한다.

```html
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');
    const $apple = $fruits.firstElementChild;

    // $apple 요소를 얕은 복사하여 사본을 생성. 텍스트 노드가 없는 사본이 생성된다.
    const $shallowClone = $apple.cloneNode();
    // 사본 요소 노드에 텍스트 추가
    $shallowClone.textContent = 'Banana';
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($shallowClone);

    // #fruits 요소를 깊은 복사하여 모든 자손 노드가 포함된 사본을 생성
    const $deepClone = $fruits.cloneNode(true);
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($deepClone);
  </script>
</html>
```

<br>

#### 39.6.8 노드 교체

---

- Node.prototype.replaceChild(newChild, oldChild) 메서드는 자신을 호출한 노드의 자식 노드를 다른 노드로 교체한다.

```html
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 기존 노드와 교체할 요소 노드를 생성
    const $newChild = document.createElement('li');
    $newChild.textContent = 'Banana';

    // #fruits 요소 노드의 첫 번째 자식 요소 노드를 $newChild 요소 노드로 교체
    $fruits.replaceChild($newChild, $fruits.firstElementChild);
  </script>
</html>
```

<br>

#### 39.6.9 노드 삭제

---

- Node.prototype.removeChild(child) 메서드는 child 매개변수에 인수로 전달한 노드를 DOM에서 삭제한다.

```html
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // #fruits 요소 노드의 마지막 요소를 DOM에서 삭제
    $fruits.removeChild($fruits.lastElementChild);
  </script>
</html>
```

<br>

### 39.7 어트리뷰트

#### 39.7.1 어트리뷰트 노드와 attributes 프로퍼티

---

- HTML 문서의 구성 요소인 HTML 요소는 여러 개의 어트리뷰트를 가질 수 있다.
- HTML 요소의 동작을 제어하기 위한 추가적인 정보를 제공한다.
- 대부분의 어트리뷰트는 모든 HTML 요소에서 사용될 수 있지만, type, value, checked 어트리뷰트는 input 요소에만 사용할 수 있는 경우도 있다.
- HTML 문서가 파싱될 때 HTML 요소의 어트리뷰트는 어트리뷰트 노드로 변환되어 요소 노드와 연결된다. 

<br>

#### 39.7.2 HTML 어트리뷰트 조작

---

- HTML 어트리뷰트 값을 참조하려면 Element.prototype.getAttribute(attributeName) 메서드를 사용하고, HTML 어트리뷰트 값을 변경하려면 Element.prototype.setAttribute(attributeName, attributeValue) 메서드를 사용한다.

```html
<html>
<body>
  <input id="user" type="text" value="ungmo2">
  <script>
    const $input = document.getElementById('user');

    // value 어트리뷰트 값을 취득
    const inputValue = $input.getAttribute('value');
    console.log(inputValue); // ungmo2

    // value 어트리뷰트 값을 변경
    $input.setAttribute('value', 'foo');
    console.log($input.getAttribute('value')); // foo
  </script>
</body>
</html>
```



<br>

