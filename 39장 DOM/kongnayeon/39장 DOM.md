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