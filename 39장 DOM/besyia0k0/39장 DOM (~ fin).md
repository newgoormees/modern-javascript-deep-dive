# 39장. DOM (~ fin)

## 목차

- [39.5 요소 노드의 텍스트 조작](#395-요소-노드의-텍스트-조작)
  * [39.5.1 nodeValue](#3951-nodevalue)
  * [39.5.2 textContent](#3952-textcontent)
  * [39.5.3 innerText](#3953-innertext)
- [39.6 DOM 조작](#396-dom-조작)
  * [39.6.1 innerHTML](#3961-innerhtml)
  * [39.6.2 insertAdjacentHTML](#3962-insertadjacenthtml)
  * [39.6.3 노드 생성과 추가](#3963-노드-생성과-추가)
  * [39.6.4 복수의 노드 생성과 추가](#3964-복수의-노드-생성과-추가)
  * [39.6.5 노드 삽입](#3965-노드-삽입)
  * [39.6.6 노드 이동](#3966-노드-이동)
  * [39.6.7 노드 복사](#3967-노드-복사)
  * [39.6.8 노드 교체](#3968-노드-교체)
  * [39.6.9 노드 삭제](#3969-노드-삭제)
- [39.7 어트리뷰트](#397-어트리뷰트)
  * [39.7.1 어트리뷰트 노드와 attributes 프로퍼티](#3971-어트리뷰트-노드와-attributes-프로퍼티)
  * [39.7.2 HTML 어트리뷰트 조작](#3972-html-어트리뷰트-조작)
  * [39.7.3 HTML 어트리뷰트 vs DOM 프로퍼티](#3973-html-어트리뷰트-vs-dom-프로퍼티)
  * [39.7.4 data 어트리뷰트와 dataset 프로퍼티](#3974-data-어트리뷰트와-dataset-프로퍼티)
- [39.8 스타일](#398-스타일)
  * [39.8.1 인라인 스타일 조작](#3981-인라인-스타일-조작)
  * [39.8.2 클래스 조작](#3982-클래스-조작)
  * [39.8.3 요소에 적용되어 있는 CSS 스타일 참조](#3983-요소에-적용되어-있는-css-스타일-참조)
- [39.9 DOM 표준](#399-dom-표준)

---

## 39.5 요소 노드의 텍스트 조작

setter, getter 모두 존재하는 접근자 프로퍼티들에 대해서 알아보자.

### 39.5.1 nodeValue

노드 객체의 값, 즉 텍스트 노드의 **텍스트**를 반환한다. 텍스트노드가 아닌 노드를 참조하면 null을 반환한다.

텍스트 노드는 텍스트를 변경할 요소 노드의 자식 노드이기 때문에, `firstChild` 등의 메서드로 탐색한 후 변경할 수 있다.

```html
<!DOCTYPE html>
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

### 39.5.2 textContent

요소 노드의 콘텐츠 영역(시작 태그 ~ 끝 태그) 내의 텍스트 노드의 값을 모두 반환한다.

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // #foo 요소 노드의 텍스트를 모두 취득한다. 이때 HTML 마크업은 무시된다.
    console.log(document.getElementById('foo').textContent); // Hello world!
  </script>
</html>
```

해당 프로퍼티에 문자열을 할당하면, 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가된다. 문자열 안에 HTML 마크업이 존재하더라도 파싱되지 않고 문자열 그대로 인식된다.

### 39.5.3 innerText

`textContent`와 유사하게 동작하지만, 다음 단점때문에 사용하지 않는 것이 좋다.

- CSS에 순종적이기에, `visibility: hidden` 인 노드의 텍스트는 반환되지 않는다.
- CSS를 고려해야하기 때문에 `textContent`보다 느리게 동작한다.

## 39.6 DOM 조작

새로운 노드를 생성하여 DOM에 추가하거나 기존 노드를 삭제 혹은 교체하는 것이다.

DOM 조작을 하게 되면 리플로우와 리페인트(렌더링 및 페인팅)가 발생하기 때문에 성능에 영향이 생긴다.

getter, setter 모두 존재하는 접근자 프로퍼티들에 대해서 알아보자.

### 39.6.1 innerHTML

요소 노드의 HTML 마크업을 취득하거나 변경한다.

요소 노드의 콘텐츠 영역(시작 태그 ~ 끝 태그)의 모든 HTML 마크업을 문자열로 반환한다.

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // #foo 요소의 콘텐츠 영역 내의 HTML 마크업을 문자열로 취득한다.
    console.log(document.getElementById('foo').innerHTML);
    // "Hello <span>world!</span>"
  </script>
</html>
```

해당 프로퍼티에 문자열을 할당하면, 문자열에 포함되어 있는 HTML 마크업이 파싱되어 DOM에 반영된다.

이 때, HTML 마크업 내에 자바스크립트 악성 코드가 포함되어 있다면, 파싱 과정에서 이것이 실행될 수 있기 때문에 **크로스 사이트 스크립팅 공격(XSS)** 에 취약하다. HTML5에서는 `inneerHTML` 프로퍼티로 삽입된 script 요소 내의 자바스크립트 코드를 실행하지 않지만, 해당 요소 없이도 공격은 가능하다.

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 에러 이벤트를 강제로 발생시켜서 자바스크립트 코드가 실행되도록 한다.
    document.getElementById('foo').innerHTML
      = `<img src="x" onerror="alert(document.cookie)">`;
  </script>
</html>
```

이러한 부분은 **HTML 새니티제이션(sanitization)** 으로 위험 제거가 가능하다. `DOMPurity` 라이브러리 추천

```jsx
DOMPurity.sanitize('<img src="x" onerror="alert(document.cookie)">');
// <img src="x">
```

`innerHTML` 을 통해 변경했을 때의 단점 중 하나는 기존의 자식 노드를 모두 제거하고 문자열을 파싱하여 DOM을 변경한다는 것이다. 문자열의 일부만 변경하여 일부 노드는 변경이 없더라도 전부 새롭게 생성한다.

```html
<!DOCTYPE html>
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
  </script>
</html>
```

또한 새로운 요소를 중간에 삽입 시 삽입 위치를 지정하기 힘들다는 단점도 존재한다.

### 39.6.2 insertAdjacentHTML

`Element.prototype.insertAdjacentHTML(position, DOMString)`

기존 요소를 제거하지 않으면서 위치를 지정해 HTML 마크업 문자열을 파싱하여 새로운 요소를 삽입한다.

위치는 다음 네 가지가 존재한다: `beforebegin`, `afterbegin`, `beforeend`, `afterend` (태그 앞뒤)

```html
<!DOCTYPE html>
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

역시 HTML 마크업 문자열 파싱을 진행하므로 크로스 사이트 스크립팅 공격에 약하다.

### 39.6.3 노드 생성과 추가

노드를 직접 생성, 삽입, 삭제하는 메서드들에 대해서 알아보자.

```html
<!DOCTYPE html>
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

위 방식대로 하면, 맨 마지막 줄에서만 DOM 변경이 이뤄지기 때문에 리플로우와 리페인트가 한 번만 동작하여 효율적이다.

- **요소 노드 생성**
    
    `Document.prototype.createElement(tagName)`
    
    태그 이름을 나타내는 문자열을 인수로 전달한다. 이렇게 만들어진 요소 노드는 아무런 자식 노드가 없다.
    
- **텍스트 노드 생성**
    
    `Document.prototype.createTextNode(text)`
    
    텍스트 노드의 값으로 사용할 문자열을 인수로 전달한다.
    
- **노드의 자식 추가**
    
    `Document.prototype.appendChild(childNode)`
    
    인수로 전달한 노드를 호출한 노드의 마지막 자식 노드로 추가한다.
    
    요소 노드에 자식 노드가 하나도 없는 경우, `textContent` 프로퍼티를 사용하는 편이 더욱 간편하다.
    

### 39.6.4 복수의 노드 생성과 추가

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    ['Apple', 'Banana', 'Orange'].forEach(text => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
      $fruits.appendChild($li);
    });
  </script>
</html>
```

위 코드에서는 DOM 변경이 3번 이루어지고, 리플로우와 리페인트가 3번 실행된다.

DOM 변경은 높은 비용이 들기 때문에 가급적 줄이는 편이 좋고, 따라서 DOM에 요소 노드를 반복하여 추가하는 것은 비효율적이다. 이 문제를 해결하기 위해 **컨테이너 요소**를 사용한다.

컨테이너 요소가 div 등의 태그라면, 불필요한 컨테이너 요소도 DOM에 추가되기 때문에, `DocumentFragment` 노드를 사용한다. `DocumentFragment` 노드를 DOM에 추가하면 자신은 제거되고 자신의 자식 노드만 DOM에 추가된다.

`Document.prototype.createDocumentFragment`

```html
<!DOCTYPE html>
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

### 39.6.5 노드 삽입

- **마지막 노드로 추가**
    
    `Node.prototype.appendChild(newNode)`
    
- **지정한 위치에 노드 삽입**
    
    `Node.prototype.insertBefore(newNode, childNode)`
    
    childNode의 앞에 newNode를 삽입한다. childNode가 `insertBefore` 메서드를 호출한 노드의 자식 노드가 아닐 경우, `DOMException` 에러가 발생한다. 또한 childNode가 null인 경우, `appendChild`와 동일하게 동작한다.
    

### 39.6.6 노드 이동

이미 존재하는 노드를 `appendChild` 또는 `insertBefore` 을 사용하여 DOM에 다시 추가하면, 현재 위치에서 노드를 제거하고 새로운 위치에 노드를 추가한다. 즉, 노드가 이동된다.

### 39.6.7 노드 복사

`Node.prototype.cloneNode([deep: true | false])`

`deep`이 `true`인 경우, 깊은 복사를 진행하여 모든 자손 노드가 포함된 사본을 생성한다.

`deep`이 `false`인 경우, 얕은 복사를 진행하여 노드 자신만의 사본을 생성한다(자식 노드를 복사하지 않음).

```html
<!DOCTYPE html>
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

### 39.6.8 노드 교체

`Node.prototype.replaceChild(newChild, oldChild)`

자신을 호출한 노드의 자식 노드를 다른 노드로 교체한다. `oldChild`는 메서드를 호출한 노드의 자식 노드여야 하며, DOM에서 제거된다.

### 39.6.9 노드 삭제

`Node.prototype.removeChild(child)`

인수로 전달한 노드를 DOM에서 삭제한다. 이 노드는 메서드를 호출한 노드의 자식 노드여야 한다.

## 39.7 어트리뷰트

### 39.7.1 어트리뷰트 노드와 attributes 프로퍼티

**모든 HTML 요소에서 공통적으로 사용한 어트리뷰트**

- 글로벌 어트리뷰트
    
    `id`, `class`, `style`, `title`, `lang`, `tabindex`, `draggable`, `hidden` 등
    
- 이벤트 핸들러 어트리뷰트
    
    `onclick`, `onchange`, `onfocus`, `onblur`, `oninput`, `onkeypress`, `onkeydown`, `onkeyup`, `onmouseover`, `onsubmit`, `onload` 등
    

**특정 HTML 요소에만 사용 가능한 어트리뷰트**

- input 요소에만 사용 가능한 어트리뷰트
    
    `type`, `value`, `checked` 등
    

HTML 파싱 시, HTML 어트리뷰트 당 하나의 어트리뷰트 노드가 생성된다.

한 요소 노드의 모든 어트리뷰트는 유사 배열 객체이자 이터러블인 `NamedNodeMap` 객체에 담겨 요소 노드의 `attributes` 프로퍼티에 저장된다. 따라서, `Element.prototype.attributes` 프로퍼티로 취득 가능하다(읽기 전용 접근자 프로퍼티).

```html
<!DOCTYPE html>
<html>
<body>
  <input id="user" type="text" value="ungmo2">
  <script>
    // 요소 노드의 attribute 프로퍼티는 요소 노드의 모든 어트리뷰트 노드의 참조가 담긴 NamedNodeMap 객체를 반환한다.
    const { attributes } = document.getElementById('user');
    console.log(attributes);
    // NamedNodeMap {0: id, 1: type, 2: value, id: id, type: type, value: value, length: 3}

    // 어트리뷰트 값 취득
    console.log(attributes.id.value); // user
    console.log(attributes.type.value); // text
    console.log(attributes.value.value); // ungmo2
  </script>
</body>
</html>
```

### 39.7.2 HTML 어트리뷰트 조작

`Element.prototype.getAttribute(attributeName)` / `Element.prototype.setAttribute(attributeName, attributeValue)` / `Element.prototype.hasAttribute(attributeName)` / `Element.prototype.removeAttribute(attributeName)`

### 39.7.3 HTML 어트리뷰트 vs DOM 프로퍼티

요소 노드 객체에는 HTML 어트리뷰트에 대응하는 DOM 프로퍼티가 존재한다. 이는 HTML 어트리뷰트 값을 초기값으로 가지고 있다.

DOM 프로퍼티는 getter, setter 모두 존재하는 접근자 프로퍼티이기 때문에 참조와 변경이 가능하다.

```html
<!DOCTYPE html>
<html>
<body>
  <input id="user" type="text" value="ungmo2">
  <script>
    const $input = document.getElementById('user');

    // 요소 노드의 value 프로퍼티 값을 변경
    $input.value = 'foo';

    // 요소 노드의 value 프로퍼티 값을 참조
    console.log($input.value); // foo
  </script>
</body>
</html>
```

이처럼 HTML 어트리뷰트는 다음과 같이 DOM에서 중복 관리되는 것처럼 보인다.

- 요소 노드의 attributes 프로퍼티에서 관리
- HTML 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티

하지만 그렇지 않다: 요소 노드는 초기 상태와 최신 상태를 모두 관리해야 하고, 다음과 같이 관리된다.

- 초기 상태: 어트리뷰트 노드
- 최신 상태: DOM 프로퍼티

```html
<!DOCTYPE html>
<html>
<body>
  <input id="user" type="text" value="ungmo2">
  <script>
    const $input = document.getElementById('user');

    // DOM 프로퍼티에 값을 할당하여 HTML 요소의 최신 상태를 변경한다.
    $input.value = 'foo';
    console.log($input.value); // foo

    // getAttribute 메서드로 취득한 HTML 어트리뷰트 값, 즉 초기 상태 값은 변하지 않고 유지된다.
    console.log($input.getAttribute('value')); // ungmo2
  </script>
</body>
</html>
```

- **어트리뷰트 노드**
    
    HTML 어트리뷰트로 지정한 HTML 요소의 **초기 상태**는 어트리뷰트 노드에서 관리한다.
    
    초기 상태 값을 취득하거나 변경하기 위해서는 `getAttribute` / `setAttribute` 메서드를 사용한다.
    
- **DOM 프로퍼티**
    
    사용자가 입력한 **최신 상태**는 요소 노드의 DOM 프로퍼티가 관리한다.
    
    DOM 프로퍼티에 값을 할당하는 것은 HTML 요소의 최신 상태 값을 변경하는 것을 의미하며, HTML 요소에 지정한 어트리뷰트 값에는 어떠한 영향을 주지 않는다.
    
    사용자 입력에 의한 상태 변화와 관계 없는 DOM 프로퍼티는 항상 어트리뷰트와 동일한 값을 가진다.
    
<br/>

**HTML 어트리뷰트와 DOM 프로퍼티의 대응 관계**

대부분의 HTML 어트리뷰트는 동일한 이름을 가진 DOM 프로퍼티와 1:1로 대응한다.

하지만 언제나 1:1로 대응하지는 않으며, HTML 어트리뷰트의 이름과 DOM 프로퍼티의 키가 반드시 일치하지는 않는다.
- 어트리뷰트 이름은 대소문자를 구분하지 않으나, 프로퍼티 키는 카멜 케이스를 따른다.

<br/>

**DOM 프로퍼티 값의 타입**

`getAttribute` 메서드로 취득한 **어트리뷰트** 값은 언제나 **문자열**인 반면, DOM **프로퍼티**로 취득한 최신 상태 값은 **문자열이 아닐 수도** 있다. checkbox 요소의 checked 어트리뷰트 값은 문자열이지만, checked 프로퍼티 값은 불리언 타입이다.

### 39.7.4 data 어트리뷰트와 dataset 프로퍼티

`data` 어트리뷰트와 `dataset` 프로퍼티를 사용하여 HTML 요소에 정의한 사용자 정의 어트리뷰트와 자바스크립트 간에 데이터를 교환할 수 있다. `data` 어트리뷰트는 `data-` 접두사를 가져야 한다.

data 어트리뷰트의 값은 `HTMLElement.dataset` 프로퍼티로 취득할 수 있고, 반환된 `DOMStringMap` 객체는 data 어트리뷰트의 접두사 뒤에 붙인 이름을 카멜 케이스로 변환한 프로퍼티를 가지고 있다.

이들을 통해서 `data` 어트리뷰트의 취득 혹은 변경, 추가가 가능하다.

```html
<!DOCTYPE html>
<html>
<body>
  <ul class="users">
    <li id="1" data-user-id="7621" data-role="admin">Lee</li>
    <li id="2" data-user-id="9524" data-role="subscriber">Kim</li>
  </ul>
  <script>
    const users = [...document.querySelector('.users').children];

    // user-id가 '7621'인 요소 노드를 취득한다.
    const user = users.find(user => user.dataset.userId === '7621');
    // user-id가 '7621'인 요소 노드에서 data-role의 값을 취득한다.
    console.log(user.dataset.role); // "admin"

    // user-id가 '7621'인 요소 노드의 data-role 값을 변경한다.
    user.dataset.role = 'subscriber';
    // dataset 프로퍼티는 DOMStringMap 객체를 반환한다.
    console.log(user.dataset); // DOMStringMap {userId: "7621", role: "subscriber"}
  </script>
</body>
</html>
```

## 39.8 스타일

### 39.8.1 인라인 스타일 조작

`HTMLElement.prototype.style` 프로퍼티는 getter, setter 모두 존재하는 접근자 프로퍼티이다.

다양한 CSS 프로퍼티에 대응하는 `CSSStyleDeclaration` 타입의 객체를 통해 요소 노드의 **인라인 스타일**을 취득하거나 추가, 변경한다.

CSS 프로퍼티는 케밥 케이스, `CSSStyleDeclaration` 객체는 카멜 케이스를 따른다.

```jsx
$div.style.backgroundColor = 'yellow';
$div.style['background-color'] = 'yellow';
```

CSS 프로퍼티의 값은 반드시 단위를 지정해야 한다.

### 39.8.2 클래스 조작

클래스 선택자를 사용하여 CSS 클래스를 정의한 다음, HTML 요소의 `class` 어트리뷰트값을 변경하여 HTML 요소의 스타일을 변경할 수도 있다.

단, `class` 어트리뷰트에 대응하는 DOM 프로퍼티는 `className` 과 `classList` 다. 자바스크립트에서 `class`는 예약어이기 때문이다.

- **className**
    
    `Element.prototype.className`
    
    getter, setter 모두 존재하는 접근자 프로퍼티로서 HTML 요소의 class 어트리뷰트 값을 문자열의 형태로 취득하거나 변경한다.
    
    문자열이기 때문에 공백으로 구분된 여러 개의 클래스를 반환하는 경우 다루기가 불편하다.
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
      <style>
        .box {
          width: 100px; height: 100px;
          background-color: antiquewhite;
        }
        .red { color: red; }
        .blue { color: blue; }
      </style>
    </head>
    <body>
      <div class="box red">Hello World</div>
      <script>
        const $box = document.querySelector('.box');
    
        // .box 요소의 class 어트리뷰트 값을 취득
        console.log($box.className); // 'box red'
    
        // .box 요소의 class 어트리뷰트 값 중에서 'red'만 'blue'로 변경
        $box.className = $box.className.replace('red', 'blue');
      </script>
    </body>
    </html>
    ```
    
- **classList**
    
    `Element.prototype.classList`
    
    class 어트리뷰트의 정보를 담은 `DOMTokenList` 객체를 반환한다. `DOMTokenList` 객체는 class 어트리뷰트의 정보를 나타내는 컬렉션 객체로서 유사 배열 객체이면서 이터러블이다.
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
      <style>
        .box {
          width: 100px; height: 100px;
          background-color: antiquewhite;
        }
        .red { color: red; }
        .blue { color: blue; }
      </style>
    </head>
    <body>
      <div class="box red">Hello World</div>
      <script>
        const $box = document.querySelector('.box');
    
        // .box 요소의 class 어트리뷰트 정보를 담은 DOMTokenList 객체를 취득
        // classList가 반환하는 DOMTokenList 객체는 HTMLCollection과 NodeList와 같이
        // 노드 객체의 상태 변화를 실시간으로 반영하는 살아 있는(live) 객체다.
        console.log($box.classList);
        // DOMTokenList(2) [length: 2, value: "box blue", 0: "box", 1: "blue"]
    
        // .box 요소의 class 어트리뷰트 값 중에서 'red'만 'blue'로 변경
        $box.classList.replace('red', 'blue');
      </script>
    </body>
    </html>
    ```
    
    `DOMTokenList` 객체는 다음과 같은 메서드를 제공한다.
    
    - `add(...className)`
    - `remove(...className)`
    - `item(index)`
    - `contains(className)`
    - `replace(oldClassName, newClassName)`
    - `toggle(className[, force])`
        
        일치하는 클래스가 존재하면 제거하고, 존재하지 않으면 추가한다.
        
    - etc…

### 39.8.3 요소에 적용되어 있는 CSS 스타일 참조

style 프로퍼티는 인라인 스타일만 반환하기 때문에, 클래스를 적용한 스타일이나 상속을 통해 암묵적으로 적용된 스타일은 참조가 불가능하다. HTML 요소에 적용되어 있는 모든 CSS 스타일을 참조해야 할 경우 `getComputedStyle` 메서드를 사용한다.

`window.getComputedStyle(element[, pseudo])` 

- 첫 번째 인수로 전달한 요소 노드에 적용되어 있는 평가된 스타일(최종적으로 적용된 스타일)을 `CSSStyleDeclaration` 객체에 담아 반환한다.
- 두 번째 인수로 `:after`, `:before` 와 같은 의사 요소를 지정하는 문자열을 전달할 수 있다.

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      color: red;
    }
    .box {
      width: 100px;
      height: 50px;
      background-color: cornsilk;
      border: 1px solid black;
    }
  </style>
</head>
<body>
  <div class="box">Box</div>
  <script>
    const $box = document.querySelector('.box');

    // .box 요소에 적용된 모든 CSS 스타일을 담고 있는 CSSStyleDeclaration 객체를 취득
    const computedStyle = window.getComputedStyle($box);
    console.log(computedStyle); // CSSStyleDeclaration

    // 임베딩 스타일
    console.log(computedStyle.width); // 100px
    console.log(computedStyle.height); // 50px
    console.log(computedStyle.backgroundColor); // rgb(255, 248, 220)
    console.log(computedStyle.border); // 1px solid rgb(0, 0, 0)

    // 상속 스타일(body -> .box)
    console.log(computedStyle.color); // rgb(255, 0, 0)

    // 기본 스타일
    console.log(computedStyle.display); // block
  </script>
</body>
</html>
```

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    .box:before {
      content: 'Hello';
    }
  </style>
</head>
<body>
  <div class="box">Box</div>
  <script>
    const $box = document.querySelector('.box');

    // 의사 요소 :before의 스타일을 취득한다.
    const computedStyle = window.getComputedStyle($box, ':before');
    console.log(computedStyle.content); // "Hello"
  </script>
</body>
</html>
```

## 39.9 DOM 표준

2018년 4월부터 WHATWG이 HTML, DOM에 대한 단일 표준을 내놓고 있다.

DOM은 4개의 레벨이 존재한다.