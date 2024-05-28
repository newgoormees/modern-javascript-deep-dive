# 48.1 모듈의 일반적 의미

- 모듈: 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각
- export: 공개가 필요한 모듈을 선택적으로 공개하는 것
- import: 모듈 사용자가 모듈이 공개한 자산을 자신의 스코프 내로 불려들여 재사용하는 것

# 48.2 자바스크립트와 모듈

- 자바스크립트는 웹페이지의 단순한 보조 기능을 처리하기 위한 제한적인 용도로 탄생했기 때문에 모듈이 성립하기 위해 필요한 파일 스코프와 import, export를 지원하지 않는다.
- CommonJS와 AMD(Asynchronous Module Definition)은 모듈 시스템을 도입하여 자바스크립트가 클라이언트 사이드에서도 사용될 수 있도록 한다.
- Node.js는 모듈 시스템을 지원하는 CommonJS를 따르므로 Node.js 환경에서 파일들은 독립적인 파일 스코프(모듈 스코프)를 갖는다.

# 48.3 ES6 모듈(ESM)

- ES6에서는 클라이언트 사이드 자바스크립트에서도 동작하는 모듈 기능을 추가했다.
- ES6 모듈의 사용법: script 태그에 type="module" 어트리뷰트를 추가한다. 이때 ESM의 파일 확장자는 mjs를 사용한다.

```html
<script type="module" src="app.mjs"></script>
```

## 48.3.1 모듈 스코프

- ESM은 독자적인 모듈 스코프를 갖는다.
- 일반적인 자바스크립트 파일은 script 태그로 분리해서 로드해도 독자적인 모듈 스코프를 갖지 않는다. 즉, 하나의 전역을 공유한다. 따라서 foo.js에서 선언한 변수 x와 bar.js에서 선언한 변수 x는 중복 선언되며 의도치 않게 x 변수의 값이 덮어써진다.(P)

```js
// foo.js
// 변수 x는 전역 변수다.
var x = 'foo';
console.log(window.x); // foo
```

```js
// boo.js
// 변수 x는 전역 변수다.
var x = 'boo';

// foo.js에서 선언한 전역 변수 x의 값이 재할당되었다.
console.log(window.x); // boo
```

```html
<!DOCTYPE html>
<html>
<body>
    <script src="foo.js"></script>
    <script src="boo.js"></script>
</body>
</html>
```

- ESM은 파일 자체의 독자적인 모듈 스코프를 제공하므로 모듈 내에서 var 키워드로 선언한 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.(S)

```mjs
// foo.mjs
var x = 'foo';
console.log(x); // foo
console.log(window.x); // undefined
```

```mjs
// boo.mjs
var x = 'boo';
console.log(x); // boo
console.log(window.x); // undefined
```

```html
<!DOCTYPE html>
<html>
<body>
    <script type="module" src="foo.mjs"></script>
    <script type="module" src="boo.mjs"></script>
</body>
</html>
```

- 확장자 mjs로 선언하면 모듈 스코프가 다르기 때문에 모듈 내에서 선언한 식별자는 모듈 외부에서 참조할 수 없다.

```mjs
// foo.mjs
var x = 'foo';
console.log(x); // foo
```

```mjs
// boo.mjs
console.log(x); // ReferenceError: x is not defined
```

```html
<!DOCTYPE html>
<html>
<body>
    <script type="module" src="foo.mjs"></script>
    <script type="module" src="boo.mjs"></script>
</body>
</html>
```

## 48.3.2 export 키워드

- 모듈은 독자적인 모듈 스코프를 갖기 때문에 모듈 내부에서 선언한 모든 식별자는 기본적으로 해당 모듈 내부애서만 참조할 수 있다.
- export 키워드를 사용하면 모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있도록 할 수 있다.

export 키워드를 사용하는 방법

1. export 키워드는 변수 함수, 클래스 등 모든 식별자의 선언문 앞에 사용한다.
2. export 할 대상을 하나의 객체로 구성하여 한 번에 export할 수 있다.

```mjs
// lib.mjs
// 1번 방식
// 변수의 공개
export const pi = Math.PI;

// 함수의 공개
exprot function square(x) = {
    return x*x;
}

// 클래스의 공개
export class Person {
    constructor(name) {
        this.name = name;
    }
}
```

```js
// lib.mjs
const pi = Math.PI;

function square(x) = {
    return x*x;
}

class Person {
    constructor(name) {
        this.name = name;
    }
}

// 변수, 함수, 클래스를 하나의 객체로 구성하여 공개
export { pi, square, Person};
```

## 48.3.3 import 키워드

- import 키워드를 사용하여 다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드할 수 있다. 이때 다른 모듈이 export한 식별자 이름으로 import 해야 하머 ESM의 경우 파일 확장자를 생략할 수 있다.
- 아래 코드에서 app.mjs는 애플리케이션의 진입점이므로 반드시 script 태그로 로드해야 한다.
- lib.mjs는 app.mjs에 의해 로드되는 의존성이므로 script 태그로 로드하지 않아도 된다.

```js
// app.mjs
// 같은 폴더 내의 lib.js 모듈이 export한 식별자 이름으로 import 해야 한다.
// ESM의 경우 파일 확장자를 생략핡 수 있다.
import { pi, square, Person } from './lib.mjs';

console.log(pi); // 3.141592653589793
console.log(square(10)); // 100
console.log(new Person('Lee')); // { name: 'Lee' }
```

```html
<!DOCTYPE html>
<html>
<body>
    <script type="module" src="app.mjs"></script>
</body>
</html>
```

- as 키워드: 모듈이 export한 식별자 이름 지정하기 위한 키워드

```js
// app.mjs
// lib.mjs 모듈이 export한 모든 식별자를 lib 객체의 프로퍼티로 모아 import한다.
import * as lib from './lib.mjs';

console.log(lib.pi); // 3.141592653589793
console.log(lib.square(10)); // 100
console.log(new lib.Person('Lee')); // Person { name: 'Lee' }


// lib.mjs 모듈이 export한 식별자 이름을 일일이 변경하여 import한다.
import { pi as PI, square as sq, Person as P} from './lib.mjs';

console.log(PI);
console.log(sq(10));
console.log(new P('Kim')); // Person { name: 'Kim' }
```

- default 키워드: 모듈에서 이름 없이 하나의 값만 export하기 위한 키워드

```mjs
// lib.mjs
export default x => x * x;
```

```js
import square form './lib.mjs'
console.log(square(3)); // 9
```

## 추가) 모듈의 import 방법

import 구문을 사용하는 방법은 다양하며, 각각의 사용 방법은 특정한 경우와 필요에 따라 선택됩니다. import 구문은 모듈에서 하나 이상의 기능을 로드할 때 사용되며, 주로 export 구문과 함께 사용됩니다.

### `import {} from` 구문 vs `import ... from`

1. Named Imports (`import {} from`)
Named imports는 모듈이 export한 여러 기능 중 일부만 선택하여 로드할 때 사용됩니다. 이 방식은 모듈에서 특정 식별자(변수, 함수, 클래스 등)를 명시적으로 선택하여 가져오기 원할 때 유용합니다.

```javascript
// lib.mjs에서 여러 함수와 변수를 export 할 경우,
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

// 다른 모듈에서 위의 함수 중 일부만 가져오고 싶을 때
import { square, diag } from './lib.mjs';

console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

2. Default Imports (`import ... from`)
Default import는 모듈에서 default로 export된 단일 값을 로드할 때 사용됩니다. 이 방식은 모듈이 주로 하나의 기능을 내보내는 경우에 적합하며, 가져오는 측에서는 특정 이름을 지정하지 않고 사용할 수 있습니다. 이 이름은 로컬 모듈 내에서 자유롭게 지정할 수 있습니다.

```javascript
// lib.mjs
export default function(x) {
    return x * x;
}

// 다른 모듈에서 위의 함수를 default import 방식으로 가져올 때
import square from './lib.mjs';

console.log(square(9)); // 81
```

- Named Imports: 모듈에서 여러 기능을 export하고, 그 중 특정 기능만 선택적으로 로드하고 싶을 때 사용합니다. 이 방법을 사용하면 모듈의 여러 기능 중 필요한 것만 선택할 수 있어 메모리 효율성이 높아집니다.
- Default Imports: 모듈에서 하나의 주요 기능만을 export할 때 사용됩니다. 이 경우, 가져오는 측에서는 모듈에서 export된 기능에 대한 구체적인 이름을 몰라도 되며, 로컬에서 자유롭게 이름을 지정할 수 있습니다.

### 사용 상황에 따른 선택

- 단일 기능이나 주요 기능만을 제공하는 라이브러리 또는 모듈의 경우 default export를 사용합니다.
- 여러 기능을 제공하고, 사용자가 필요에 따라 선택적으로 기능을 로드해야 하는 경우 named export를 사용합니다.

## `Import` vs `Require`

- import는 ECMAScript 2015 (ES6)에서 도입된 키워드로, 모듈 시스템의 일부로 개발되었습니다.
- require는 Node.js의 모듈 시스템에서 사용되는 함수로, CommonJS 모듈 규격을 따릅니다.

### Import

- import는 선언적 방식으로, 정적 구문을 사용합니다. 이는 컴파일 시간에 모듈의 종속성이 해석되고, 파일이 처리됩니다.

```javascript
import { sqrt } from 'math';
```

### Require

- require는 함수 호출을 사용하여 모듈을 로드합니다. 이는 런타임 시에 모듈을 로드하고, 결과를 변수에 할당합니다.

```javascript
const sqrt = require('math').sqrt;
```

- require는 객체의 속성으로 함수를 가져오거나 전체 모듈을 객체로 가져올 때 사용됩니다.

### 로딩 방식 (Loading Mechanism)

- Import
  - import는 정적으로 모듈을 로드합니다. 이는 컴파일 타임에 모듈 경로를 해석하고, 필요한 부분만 가져올 수 있어 최적화에 유리합니다.
  
- Require
  - require는 동적으로 모듈을 로드합니다. 런타임에 모듈을 읽고 실행하기 때문에, 조건부로 모듈을 로드하는 등의 동적인 모듈 로딩이 가능합니다.

### 호환성 (Compatibility)

- Import
  - import는 ES6 이상에서 지원되며, 최신 브라우저와 최신 Node.js 버전에서 지원됩니다.
  - 브라우저에서는 <script type="module"> 태그를 사용하여 ES 모듈을 로드할 수 있습니다.
- Require
  - require는 Node.js에서 널리 사용되며, CommonJS 모듈을 지원하는 모든 환경에서 작동합니다.
  - 브라우저는 기본적으로 require를 지원하지 않으므로, Browserify나 Webpack과 같은 번들러가 필요합니다.

### 사용 시점

- Import
  - import는 파일의 최상단에서 사용되어야 하며, 조건부 로딩이 불가능합니다. 따라서 모듈의 사용 여부가 컴파일 타임에 결정되어야 합니다.
- Require
  - require는 코드의 어느 지점에서든 호출할 수 있습니다. 이는 실행 흐름에 따라 모듈을 로드할 수 있게 해주므로, 더 유연한 모듈 사용이 가능합니다.

### 최적화 (Optimization)

- Import
  - import는 트리 쉐이킹(tree shaking)을 통한 불필요한 코드 제거가 가능하다는 장점이 있습니다. 이는 빌드 과정에서 사용되지 않는 코드를 제거하여 최종 번들의 크기를 줄일 수 있습니다.
- Require
  - require는 전체 모듈을 로드하기 때문에 트리 쉐이킹을 사용할 수 없습니다. 이로 인해 불필요한 코드가 포함될 수 있으며, 최종 번들의 크기가 클 수 있습니다.
