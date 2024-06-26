# 39장 DOM

### 39.1 노드

**HTML 요소와 노드 객체**

- HTML 요소는 HTML 문서를 구성하는 개별적인 요소
- HTML 요소는 렌더링 엔진에 의해 파싱되어 DOM을 구성하는 요소 노드 객체로 변환되는데, HTML 요소의 어트리뷰트는 어트리뷰트 노드로, HTML 요소의 텍스트 콘텐츠는 텍스트 요소로 변환된다.
- HTML 문서는 HTML 요소들의 집합으로 이뤄지며, HTML 요소는 중첩 관계를 가짐.
    - HTML 요소의 콘텐츠에는 텍스트 뿐만 아니라 다른 HTML 요소도 포함할 수 있음
- HTML 요소 간에는 중첩 관계에 의해 계층적인 부자 관계가 형성되며, 이러한 부자 관계를 반영하여 트리 자료구조가 구성됨
- 트리 자료구조
    - 트리 자료구조는 노드들의 계층 구조로 이뤄짐
    - 부모 노드, 자식 노드로 구성되어 노드 간의 계층적 구조를 표현하는 비선형 자료구조
    - 하나의 최상위 노드(루트 노드)에서 시작하고, 루트 노드는 0개 이상의 자식 노드를 가짐.
    - 자식 노드가 없는 노드를 리프 노드라 함
    - 노드 객체들로 구성된 트리 자료구조를 DOM이라 함. 트리로 구조화되어 있기 때문에 DOM을 DOM 트리라고 부르기도 함

**노드 객체의 타입**

- DOM은 노드 객체의 계층적인 구조로 구성된다.
- 노드 객체는 종류가 있고 상속 구조를 갖는다. 총 12개의 종류가 있고, 중요한 것은 4가지 정도가 있다.
    - **문서 노드**: DOM 트리의 최상위에 존재하는 루트 노드로서, document 객체를 가리킴. 전역 객체 window의 document 프로퍼티에 바인딩되어 있음. HTML 문서당 document 객체는 유일함.
    - **요소 노드**: HTML 요소를 가리키는 객체. 요소 간의 중첩에 의해 부자 관계를 가짐.
    - **어트리뷰트 노드**: HTML 요소의 어트리뷰트를 가리키는 객체. 어트리뷰트 노드는 어트리뷰트가 지정된 HTML 요소의 요소 노드와 연결되어 있음. 어트리뷰트 노드에 접근하여 어트리뷰트를 참조하거나 변경하려면 먼저 요소 노드에 접근해야 한다.
    - **텍스트 노드**: HTML 요소의 텍스트를 가리키는 객체. 요소 노드는 문서의 구조, 텍스트 노드는 문서의 정보를 표현. 요소 노드의 자식 노드이며 자식 노드를 가질 수 없는 리프 노드임. 즉, DOM 트리의 최종단임.

**노드 객체의 상속 구조**

- DOM은 HTML 문서의 계층적 구조와 정보를 포현하며, 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조임. 즉 DOM을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 DOM API를 사용할 수 있음. → 노드 객체는 자신의 부모, 형제, 자식을 탐색할 수 있으며, 자신의 어트리뷰트와 텍스트를 조작할 수 있음.
- DOM을 구성하는 노드 객체는 표준 빌트인 객체가 아니라 브라우저 환경에서 추가적으로 제공하는 호스트 객체
- 노드 객체 또한 자바스크립트 객체이므로 프로토타입에 의한 상속 구조를 가짐
- 모든 노드 객체는 Object, EventTarget, Node 인터페이스를 상속받음
    - 문서 노드는 Document, HTML Document 인터페이스를 상속받음
    - 어트리뷰트 노드는 Attr를 상속받음
    - 텍스트 노드는 CharacterData 인터페이스를 상속받음
    - 요소 노드는 Element 인터페이스를 상속받음
- DOM은 HTML 문서의 계층적 구조와 정보를 표현하는 것은 물론 노드 객체의 종류, 즉 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API로 제공한다. 이 DOM API를 통해 HTML의 구조나 내용 또는 스타일 등을 동적으로 조작할 수 있다.

### 39.2  요소 노드 취득

- HTML의 구조나 내용 또는 스타일 등을 동적으로 조작하려면 먼저 요소 노드를 취득해야 한다.
- 요소 노드의 취득은 HTML 요소를 조작하는 시작점 → DOM은 요소 노드를 취득할 수 있는 다양한 메서드를 제공

**id를 이용한 요소 노드 취득**

- `Document.prototype.getElementById` 메서드는 인수로 전달한 id 어트리뷰트 값을 갖는 하나의 요소 노드를 탐색하여 반환
- id 값은 HTML 문서 내에서 유일한 값이어야 하며, class 어트리뷰트와는 달리 공백 문자로 구분하여 여러 개의 값을 가질 수 없음.
- HTML 문서 내에 중복된 id 값을 갖는 HTML 요소가 여러 개 존재하더라도 에러가 발생하지 않음. 이러한 경우 `getElementById` 메서드는 인수로 전달된 id 값을 갖는 첫 번째 요소 노드만 반환함.
- `Document.prototype.getElementById` 메서드는 언제나 단 하나의 요소 노드를 반환함

**태그 이름을 이용한 요소 노드 취득**

- `Document.prototype/Element.prototype.getElementByTagName` 메서드는 인수로 전달한 태그 이름을 갖는 모든 요소 노드들을 탐색하여 반환함.
- 여러 개의 요소 노드 객체를 갖는 DOM 컬렉션 객체인 `HTMLCollection` 객체(유사 배열 객체이면서 이터러블)를 반환함.
- 인수로 *를 전달하면 HTML 문서의 모든 요소 노드를 취득할 수 있음.
- Document를 통해 호출하면 DOM 전체에서 요소 노드를 탐색하여 반환
- Element를 통해 호출하면 특정 요소 노드의 자손 노드 중에서 요소 노드를 탐색하여 반환

**class를 이용한 요소 노드 취득**

- `Document.prototype/Element.prototype.getElementsByClassName` 메서드는 인수로 전달할 class 어트리뷰트 값을 갖는 모든 요소 노드들을 탐색하여 반환한다.
- 인수로 전달할 class 값은 공백으로 구분하여 여러 개의 class를 지정할 수 있다.
- getElementsByTagName 메서드와 마찬가지로 HTMLCollection 객체를 반환한다
- 특정 요소 노드를 통해 호출하며 특정 요소 노드의 자손 노드 중에서 요소 노드를 탐색하여 반환한다.

**CSS 선택자를 이용한 요소 노드 취득**

- CSS 선택자는 스타일을 적용하고자 하는 HTML 요소를 특정할 때 사용하는 문법이다.
- `Document.prototype/Element.prototype.querySelector` 메서드는 인수로 전달한 CSS 선택자를 만족시키는 하나의 요소 노드를 탐색하여 반환한다.
- `Document.prototype/Element.prototype.querySelectorAll` 메서드는 인수로 전달한 CSS 선택자를 만족시키는 모든 요소 노드를 탐색하여 반환한다.
    - 여러 개의 요소 노드 객체를 갖는 DOM 컬렉션 객체인 NodeList 객체를 반환한다. NodeList 객체는 유사 배열 객체이면서 이터러블이다.

**특정 요소 노드를 취득할 수 있는지 확인**

- `Element.prototype.matches` 메서드는 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인한다.
- `Element.prototype.matches` 메서드는 이벤트 위임을 사용할 때 유용하다.

**HTMLCollection과 NodeList**

- DOM 컬렉션 객체인 HTMLCollection과 NodeList는 DOM API가 여러 개의 결과값을 반환하기 위한 DOM 컬렉션 객체. 모두 유사 배열 객체이면서 이터러블
- 노드 객체의 상태 변화를 실시간으로 반영하는 살아 있는 객체임
- childNodes 프로퍼티가 반환하는 NodeList 객체는 HTMLCollection 객체와 같이 실시간으로 노드 객체의 상태 변경을 반영하는 live 객체로 동작하므로 주의가 필요하다.
- 노드 객체의 상태 변경과 상관없이 안전하게 DOM 컬렉션을 사용하려면 HTMLCollection이나 NodeList 객체를 배열로 변환하여 사용하는 것을 권장한다.

### 39.3 노드 탐색

- 요소 노드를 취득한 다음, 취득한 요소 노드를 기점으로 DOM 트리의 노드를 옮겨 다니며 부모, 형제, 자식 노드 등을 탐색해야 할 때가 있다.

**공백 텍스트 노드**

- HTML요소 사이의 스페이스/탭/줄바꿈 등의 공백 문자는 텍스트 노드를 생성한다.

**자식 노드 존재 확인**

- `Node.prototype.hasChildNodes` 메서드 활용 → true/false 반환

**요소 노드의 텍스트 노드 탐색**

- 요소 노드의 텍스트 노드는 요소 노드의 자식 노드
- 요소 노드의 텍스트 노드는 firstChild 프로퍼티로 접근 가능 → 첫 번째 자식 노드 반환

**부모 노드 탐색**

- `Node.prototype.parentNode` 프로퍼티 사용
- 텍스트 노드는 리프 노드이므로 부모 노드가 텍스트 노드인 경우는 없음

**형제 노드 탐색**

- `Node.prototype.previousSibling`, `Node.prototype.nextSibling`, `Element.prototype.previousElementSibling`, `Element.prototype.nextElementSibling`

### 39.4 노드 정보 취득

- `Node.prototype.nodeType`, `Node.prototype.nodeName`

### 39.5 요소 노드의 텍스트 조작

- 위에서 살펴본 프로퍼티들은 모두 읽기 전용
- `Node.prototype.nodeValue` 프로퍼티는 참조와 할당 모두 가능 → 텍스트 노드의 값 반환
    - 문서 노드나 요소 노드는 `nodeValue`를 참조해도 null 반환
- `Node.prototype.textContent` 프로퍼티도 참조와 할당 모두 가능 → 요소 노드의 텍스트와 자손 노드의 텍스트를 모두 취득하거나 변경
    - HTML 마크업은 무시됨

### 39.6 DOM 조작

- 새로운 노드를 생성하여 DOM에 추가하거나 기존 노드를 삭제 또는 교체하는 것
- DOM 조작에 의해 새로운 노드가 추가되거나 삭제되면 리플로우와 리페인트가 발생하는 원인이 됨 → 성능 최적화를 위해 주의해서 다루어야 함

**innerHTML**

- setter, getter 모두 존재하는 접근자 프로퍼티
- 요소 노드의 HTML 마크업을 취득(문자열)하거나 변경할 수 있음.
- 요소 노드의 `innerHTML` 프로퍼티에 문자열을 할당되면 HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영된다.
- 문자열로 간단히 DOM을 조작할 수 있다.
- 사용자로부터 입력받은 데이터를 그대로 innerHTML에 할당하는 것은 XSS에 취약하므로 위험하다.
    - 악성 코드가 포함되어 있다면 파싱 과정에서 그대로 실행됨

**insertAdjacentHTML**

- 기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입 → innerHTML 프로퍼티보다 효율적이고 빠름
- XSS에 취약하다

**노드 생성과 추가**

- 생성: createElement, createTextNode, appendChild
- 삽입: appendChild(마지막 자식 노드로 추가), insertBefore

**노드 이동**

- DOM에 이미 존재하는 노드를 appendChild 또는 insertBefore 메서드를 사용하여 DOM에 다시 추가하면 현재 위치에서 노드를 제거하고 새로운 위치에 노드를 추가한다.(이동한다)

**노드 복사**

- cloneNode 메서드는 노드의 사본을 생성하여 반환한다. 매개변수 deep에 전달하는 boolean 값에 따라 깊은 복사, 얕은 복사를 지정할 수 있다.

**노드 교체**

- replaceChild는 자신을 호출한 노드의 자식 노드를 다른 노드로 교체함

**노드 삭제**

- removeChild는 child 매개변수에 전달한 노드를 DOM에서 삭제한다.

### 39.7 어트리뷰트

- HTML 요소는 여러 개의 어트리뷰트를 가질 수 있다.
- `어트리뷰트 이름=”어트리뷰트 값”` 형식으로 정의
- 모든 어트리뷰트 노드의 참조는 유사 배열 객체이자 이터러블인 NamedNodeMap 객체에 담겨서 요소 노드의 attributes 프로퍼티에 저장된다.
- getAttribute/setAttribute 메서드를 사용해 HTML 어트리뷰트 값을 취득하거나 변경할 수 있다

**HTML 어트리뷰트, DOM 프로퍼티**

- DOM 프로퍼티들은 HTML 어트리뷰트의 값을 초기값으로 가지고 있다.
- HTML 어트리뷰트의 역할은 HTML 요소의 초기 상태를 지정하는 것이다. 변하지 않는다.
- 요소 노드는 상태를 가지고 있다.(최신 상태, 초기 상태)
- 요소 노드의 초기 상태는 어트리뷰트 노드가 관리하며 요소 노드의 최신 상태는 DOM 프로퍼티가 관리한다.
- HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태는 어트리뷰트 노드에서 관리한다.
- 사용자가 입력한 최신 상태는 HTML 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티가 관리한다. DOM 프로퍼티는 사용자의 입력에 의한 상태 변화에 반응하여 언제나 최신 상태를 유지한다.
- getAttribute 메서드로 취득한 값은 언제나 문자열 이지만, DOM 프로퍼티로 취득한 최신 상태값은 문자열이 아닐 수 있다.

**data 어트리뷰트와 dataset 프로퍼티**

- **`data`** 어티리뷰트와 **`dataset`** 프로퍼티를 사용해 어트리뷰트와 자바스크립트 간에 데이터를 교환 가능
- **`data`** 어트리뷰트는 **`data-`** 접두사 뒤에 임의의 이름을 붙여 사용
- **`data`** 어트리뷰트 값은 **`HTMLElement.dataset`** 프로퍼티로 취득
- 존재 하지 않는 이름을 키로 **`dataset`** 프로퍼티에 할당하면 **`HTML`** 요소에 **`data`** 어트리뷰트가 추가

### 39.8 스타일

**인라인 스타일 조작**

- **`HTMLElement.prototype.style`** 프로퍼티
- **`getter`** / **`setter`** 가 모두 존재하는 접근자 프로퍼티로 요소 노드의 인라인 스타일을 취득하거나 추가 또는 변경

**클래스 조작**

- class 어트리뷰트에 대응하는 DOM 프로퍼티는 class 가 아니라 **`className`** 과 **`classList`**

**className**

- setter, getter 모두 존재
- HTML 요소의 class 어트리뷰트 값을 취득하거나 변경
- 클래스 값이 공백으로 구분되어 있을 경우 그대로 문자열로 반환

**classList**

- class 어트리뷰트의 정보를 담은 DOMTokenList 객체를 반환
- **`DOMTokenList`** 객체는 class 어트리뷰트의 정보를나타내는 컬렉션 객체로 유사 배열이면서 이터블

### 39.9 DOM 표준

- 4개의 레벨(버전) 존재
    - DOM Level 1
    - DOM Level 2
    - DOM Level 3
    - DOM Level 4