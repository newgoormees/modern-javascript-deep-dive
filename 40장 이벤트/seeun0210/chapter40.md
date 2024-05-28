## 1. 이벤트 드리븐 프로그래밍

### 정의

브라우저는 특정 사건을 감지하고, 사건에 반응하는 함수인 이벤트 핸들러를 호출. 이벤트 핸들러는 브라우저가 자동으로 호출하며, 개발자는 이벤트가 발생할 타이밍에 맞추어 핸들러를 등록만 해두면 됨.

### 예시

```html
<!DOCTYPE html>
<html lang="en">
<body>
  <button>Click me!</button>
  <script>
    const button = document.querySelector('button');
    button.onclick = () => {
      alert('Button clicked');
    };
  </script>
</body>
</html>

```

이 코드에서는 사용자가 버튼을 클릭할 때 알림창을 띄우는 이벤트 핸들러를 등록.

## 2. 이벤트 타입

### 2.1 마우스 이벤트

| 이벤트 타입 | 설명 |
| --- | --- |
| click | 마우스 버튼을 클릭했을 때 발생 |
| dblclick | 마우스 버튼을 더블 클릭했을 때 발생 |
| mousedown | 마우스 버튼을 눌렀을 때 발생 |
| mouseup | 마우스 버튼을 놓았을 때 발생 |
| mousemove | 마우스가 움직일 때 발생 |
| mouseenter | 마우스 포인터가 요소 경계 안으로 들어올 때 발생(버블링 없음) |
| mouseleave | 마우스 포인터가 요소를 떠날 때 발생(버블링 없음) |

### 2.2 키보드 이벤트

| 이벤트 타입 | 설명 |
| --- | --- |
| keydown | 키를 눌렀을 때 발생 |
| keypress | 키가 눌려져 있을 때 발생(특정 키에 한해) |
| keyup | 키를 놓았을 때 발생 |

### 2.3 포커스 이벤트

| 이벤트 타입 | 설명 |
| --- | --- |
| focus | 요소가 포커스를 받았을 때 발생(버블링 없음) |
| blur | 요소가 포커스를 잃었을 때 발생(버블링 없음) |
| focusin | 요소가 포커스를 받았을 때 발생(버블링 있음) |
| focusout | 요소가 포커스를 잃었을 때 발생(버블링 있음) |

## 3. 이벤트 핸들러 등록 및 제거 방법

### 3.1 어트리뷰트 방식

HTML 요소의 어트리뷰트를 사용하여 이벤트 핸들러를 직접 등록

```html
<button onclick="alert('Clicked')">Click me!</button>

```

### 3.2 프로퍼티 방식

DOM 요소의 이벤트 핸들러 프로퍼티에 함수를 직접 할당

```html
<script>
  const button = document.querySelector('button');
  button.onclick = function() {
    console.log('Button clicked');
  };
</script>

```

### 3.3 `addEventListener` 방식

`addEventListener` 메소드를 사용하여

이벤트 핸들러를 등록. 이 방식을 사용하면 동일한 이벤트에 여러 핸들러를 등록할 수 있음

```html
<script>
  const button = document.querySelector('button');
  button.addEventListener('click', function() {
    console.log('First handler');
  });
  button.addEventListener('click', function() {
    console.log('Second handler');
  });
</script>

```

### 3.4 이벤트 핸들러 제거

`removeEventListener` 메소드를 사용하여 이벤트 핸들러를 제거.

```html
<script>
  const handleClick = function() {
    console.log('Button clicked');
  };
  button.addEventListener('click', handleClick);
  button.removeEventListener('click', handleClick);
</script>

```

## 4. 이벤트 객체

이벤트 핸들러 함수에는 이벤트와 관련된 정보를 담고 있는 이벤트 객체가 자동으로 전달.

```html
<script>
  document.onclick = function(event) {
    console.log(`Mouse at ${event.clientX}, ${event.clientY}`);
  };
</script>

```
여기 이벤트 전파, 이벤트 위임, 이벤트 처리 방법 등에 관한 내용을 마크다운 문서로 정리했습니다:

---

# 이벤트 전파와 위임

## 1. 이벤트 전파 (Event Propagation)

DOM에서 발생한 이벤트는 특정 요소에서 시작해 DOM 트리를 통해 전파

### 1.1. 캡처링 단계 (Capturing Phase)

- 이벤트가 상위 요소에서 하위 요소로 전파

### 1.2. 타깃 단계 (Target Phase)

- 이벤트가 이벤트 타깃(이벤트를 발생시킨 요소)에 도달

### 1.3. 버블링 단계 (Bubbling Phase)

- 이벤트가 하위 요소에서 상위 요소로 다시 전파

```html
<ul id="fruits">
  <li id="apple">Apple</li>
  <li id="banana">Banana</li>
  <li id="orange">Orange</li>
</ul>
<script>
  document.getElementById('fruits').addEventListener('click', e => {
    console.log(`이벤트 단계: ${e.eventPhase}`);
    console.log(`이벤트 타깃: ${e.target.tagName}`);
    console.log(`커런트 타깃: ${e.currentTarget.tagName}`);
  });
</script>
```

### 주의점

- `addEventListener`의 세 번째 인자로 `true`를 설정하면 캡처링 단계에서 이벤트를 캐치할 수 있음
- `stopPropagation()` 메서드를 사용하면 이벤트 전파를 중단할 수 있음

## 2. 이벤트 위임 (Event Delegation)

상위 요소에 하나의 이벤트 리스너를 추가하여 여러 자식 요소에서 발생하는 이벤트를 처리하는 방식.

- 동적으로 추가되는 요소에 대해 개별적으로 이벤트 리스너를 추가할 필요가 없음
- 메모리 사용을 최적화하고, 리스너 수를 줄일 수 있음

### 예제 코드

```html
<ul id="fruits">
  <li>Apple</li>
  <li>Banana</li>
  <li>Orange</li>
</ul>
<script>
  document.getElementById('fruits').addEventListener('click', function(e) {
    if (e.target.tagName === 'LI') {
      alert(`You clicked on ${e.target.textContent}!`);
    }
  });
</script>
```

## 3. DOM 요소의 기본 동작 조작

### 3.1 기본 동작 중단

`preventDefault()` 메서드를 사용하여 링크 클릭, 폼 제출 등의 기본 동작을 중단할 수 있습니다.

### 3.2 이벤트 전파 방지

`stopPropagation()` 메서드를 사용하여 이벤트가 상위 또는 하위 요소로 전파되는 것을 방지할 수 있습니다.

## 4. 이벤트 핸들러 내부의 `this`

- 이벤트 핸들러 어트리뷰트 방식에서 `this`는 전역 객체 `window`를 가리킵니다.
- 프로퍼티 방식과 `addEventListener` 메서드에서 `this`는 이벤트를 바인딩한 DOM 요소를 가리킵니다.

### 화살표 함수의 사용

화살표 함수에서는 `this`가 상위 컨텍스트의 `this`를 가리키므로, 이벤트 리스너 내에서는 사용에 주의가 필요합니다.

## 5. 커스텀 이벤트

`CustomEvent` 생성자를 사용하여 사용자 정의 이벤트를 생성하고, `

dispatchEvent()` 메서드로 이벤트를 발생시킬 수 있습니다. 이벤트에는 추가적인 데이터를 담아 전달할 수 있습니다.

### 예제: 커스텀 이벤트 생성 및 디스패치

```html
<button id="myButton">Click me</button>
<script>
  const button = document.getElementById('myButton');
  button.addEventListener('customEvent', e => {
    console.log(`Event triggered: ${e.detail}`);
  });

  let event = new CustomEvent('customEvent', { detail: 'Hello World' });
  button.dispatchEvent(event);
</script>
```

