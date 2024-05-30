## 이벤트 드리븐 프로그래밍

브라우저는 특정 사건이 발생할 경우 이를 감지하여 이벤트를 발생시킴

이벤트가 발생했을 때 호출되는 함수를 `이벤트 핸들러`라 하며,

이벤트가 발생했을 때 브라우저에게 이벤트 핸들러 호출을 위임하는 것을 `이벤트 핸들러 등록`이라고 함

브라우저는 사용자의 동작을 감지하여 이벤트를 발생시키고, 그 이벤트가 발생하면 실행될 특정 핸들러를 등록할 수 있음

함수를 언제 호출할지 알 수 없으므로 개발자가 함수를 호출하지 않고, 이벤트 발생시 브라우저가 함수를 호출하도록 위임함

이 방식을 이벤트 드리븐 프로그래밍이라 함

## 이벤트 타입

### 마우스 이벤트

- click
- dbclick
- mousedown - 마우스 버튼 눌렀을 때
- mouseup - 누르던 마우스 버튼을 놓을 때
- mousemove - 마우스 커서 움직일 때
- mouseenter - 마우스 커서를 html 요소 안으로 이동시킬 때 (버블링 x)
- mouseover - 마우스 커서를 html 요소 안으로 이동시킬 때 (버블링 o)
- mouseleave - 마우스 커서를 html 요소 밖으로 이동시킬 때 (버블링 x)
- mouseout - 마우스 커서를 html 요소 밖으로 이동시킬 때 (버블링 o)

### 키보드 이벤트

- keydown - 모든 키 누를 때
- keypress - 문자 키를 눌렀을 때 연속적으로 발생
- keyup - 누르고 있던 키를 놓았을 때 한 번만 실행

### 포커스 이벤트

- focus - html요소가 포커스 받을 때 (버블링 x)
- blur
- focusin
- focusout

### 폼 이벤트

- submit - 폼 요소 내의 input, select 입력 필드에서 엔터를 눌렀을 때, 폼 요소 내의 submit버튼을 눌렀을 때

### 값 변경 이벤트

- input - text, checkbox, radio 등 요소의 값이 입력되었을 때
- change
- readystatechange

### DOM 뮤테이션 이벤트

- DOMContentLoaded - DOM생성이 완료되었을 때

### 뷰 이벤트

- resize - 브라우저 크기 변경시 연속적으로 발생
- scroll - 스크롤시 연속적으로 발생

### 리소스 이벤트

- load - DOMContentLoaded 발생 후 모든 리소스가 로딩되었을 때
- unload - 리소스가 언로드될 때
- abort - 리소스 로딩이 중단되었을 때
- error - 리소스 로딩이 실패했을 때

## 이벤트 핸들러 등록

### 이벤트 핸들러 어트리뷰트 방식

html요소의 속성에는 이벤트 핸들러 속성이 있음

`on + 이벤트 타입` 형식

어트리뷰트 값으로 함수 참조가 아닌 함수 호출문 등의 문을 할당함

함수가 아닌 값을 핸들러로 등록하면 정상적으로 동작하지 않음

### 이벤트 핸들러 프로퍼티 방식

window객체, Document, HTMLElement 타입의 dom 노드 객체는 이벤트에 대응하는 이벤트 핸들러 프로퍼티를 가지고 있음

이벤트 핸들러를 등록하려면 아래 세 가지를 지정해야 함

1. 이벤트를 발생시킬 이벤트 타깃
2. 이벤트 종류를 나타내는 이벤트 타입
3. 이벤트 핸들러

```tsx
<script>
	const $button = document.querySelector('button'); // 이벤트 타깃
	
	$button.onclick = function () { // onclick = 이벤트 타입
		console.log('button click'); // 이벤트 핸들러
	};
</script>
```

이벤트 핸들러는 이벤트 타깃 혹은 전파된 이벤트를 캐치할 DOM노드 객체에 바인딩함

### addEventListener 메서드 방식

`타깃.addEventListener(’타입’, 핸들러, [, useCapture]);`

** useCapture: 캡처 사용 여부 - true: 캡처링 / false: 버블링 (기본값)

useCapture에 따라 버블링 단계에서 이벤트를 캡처할지, 캡처링 단계에서 캡처할지가 갈림

이벤트 핸들러 프로퍼티 방식과 다르게 하나 이상의 이벤트 핸들러를 등록할 수 있음

## 이벤트 핸들러 제거

`removeEventListener`메서드를 통해 제거함

addEventListener메서드에 인수로 전달한 등록 이벤트 핸들러와 동일한 함수여야 함

⇒ 무명 함수를 등록한 경우 제거할 수 없음

이벤트 핸들러 프로퍼티 방식으로 등록한 경우 removeEventListener로 제거할 수 없으므로, 핸들러 프로퍼티에 null을 할당함

## 이벤트 객체

이벤트가 발생하면 이벤트에 관련한 정보를 담는 이벤트 객체가 동적으로 생성됨

생성된 이벤트 객체는 `(e) ⇒ {}` 와 같이 이벤트 핸들러의 첫 번째 인수로 전달됨

### 이벤트 객체의 상속 구조

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/85455d43-dfdf-4b05-9938-8915080e505d/4e0c51e2-c5da-44f4-9a1d-8260d37af8f0/Untitled.png)

Event인터페이스는 DOM내에서 발생한 이벤트에 의해 생성되는 이벤트 객체를 나타냄

여기에는 모든 이벤트 객체의 공통 프로퍼티가 정의되어 있고 FocusEvent, MouseEvent, KeyboardEvent 등의 하위 인터페이스에는 각각에 따라 고유한 프로퍼티가 정의되어 있음

### 이벤트 객체의 공통 프로퍼티

Event.prototype에 정의된 이벤트 관련 프로퍼티는 MouseEvent등 모든 파생 이벤트 객체에 상속됨

공통 프로퍼티는 아래와 같음

- type - 이벤트 타입
- target - 이벤트를 발생시킨 DOM요소
- currentTarget - 이벤트 핸들러가 바인딩된 DOM요소
- eventPhase - 이벤트 전파 단계
    - 0: 이벤트 없음
    - 1: 캡처링
    - 2: 타깃
    - 3: 버블링
- bubbles - 이벤트를 버블링으로 전파하는지 여부
- defaultPrevented - preventDefault 메서드를 호출하여 이벤트를 취소했는지 여부
- isTrusted - 사용자의 행위에 의한 이벤트인지 여부
- timeStamp - 이벤트 발생 시각

예) 체크박스 클릭

체크 상태 변경 → checked프로퍼티 값 변경 → change 이벤트 발생 → Event타입의 이벤트 객체 생성

이때 이벤트 객체의 target프로퍼티는 이벤트를 발생시킨 객체를 의미함 = $checkbox

currentTarget은 이벤트 핸들러가 바인딩된 DOM요소를 나타냄

위 경우는 currentTarget과 tartget이 동일하지만, 다를 수 있음

### 마우스 정보 취득

MouseEvent타입의 이벤트 객체는 다음과 같은 고유 프로퍼티를 가짐

- 마우스 포인터의 좌표 정보 프로퍼티 - screenX, screenY, …
- 버튼 정보 프로퍼티 - altKey, ctrlKey, …

예) DOM 요소 드래그하여 이동

mousedown 이벤트 → mouseup이벤트 발생하는 동안이 드래그

각 이벤트간의 발생 시점의 좌표를 비교하여 드래그 대상 이동 거리 계산

## 이벤트 전파

DOM트리 상에 존재하는 DOM요소 노드에서 발생한 이벤트는 DOM트리를 통해 전파됨

= `이벤트 전파`

이벤트 객체가 생성되고 이벤트를 발생시킨 DOM요소인 이벤트 타깃을 중심으로 DOM트리를 통해 이벤트가 전파됨

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/85455d43-dfdf-4b05-9938-8915080e505d/d5953144-9da0-46bf-97fe-8d6e43c76b71/Untitled.png)

만약 li 요소를 클릭하면 클릭 이벤트가 발생되고, 이때 생성된 이벤트가 DOM트리를 통해 전파됨

아래와 같은 3단계로 구성됨

1. 캡처링 - 이벤트가 상위 요소에서 하위 요소 방향으로 전파
2. 타겟 - 이벤트가 이벤트 타깃에 도달
3. 버블링 - 이벤트가 하위 요소에서 상위 요소 방향으로 전파

예) ul 에 이벤트 핸들러 바인딩하고 ul의 하위 요소인 li를 클릭하여 이벤트 발생

이벤트 타겟: li

커런트 타겟: ul (핸들러 바인딩)

li 클릭

→ 클릭 이벤트 객체 생성

→ li가 이벤트 타깃

→ 클릭 이벤트 객체가 window에서 시작하여 이벤트 타깃인 li 방향으로 전파(캡처링)

→ 이벤트 객체가 이벤트를 발생시킨 이벤트 타겟에 도달 (타깃)

→ 이벤트 객체가 이벤트 타깃으로부터 window방향으로 전파(버블링)

이벤트는 이벤트 타깃뿐 아니라 상위 DOM요소에서도 캐치할 수 있음

대부분의 이벤트는 캡처링과 버블링을 통해 전파되지만, 포커스 이벤트(focus/blur), 리소스 이벤트(load/aboart/error), 마우스 이벤트(mouseenter/mouseleave)는 버블링을 통해 전파되지 않음 (event.bubbles=false)

버블링되지 않는 이벤트는 타깃의 상위 요소에서 캐치하려면 캡처링 단계의 이벤트를 캐치해야 함. 그러나 위 이벤트들은 대체할 수 있는 focusin/focusout등의 이벤트가 존재하므로 이런 경우는 거의 없음

### 이벤트 위임
