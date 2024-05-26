# 21장 빌트인 객체

## 21.1 자바스크립트 객체의 분류

### 1. 표준 빌트인 객체

ECMAScript 사양에 정의된 객체→ 실행 환경과 관계없이 언제나 사용 가능

애플리케이션 전역의 공통 기능을 제공

별도의 선언 없이 전역 변수 처럼 언제나 참조될 수 있음

### 2. 호스트 객체

ECMAScript 사양에 정의되어 있지는 않지만 자바스크립트 실행환경(브라우저 환경 또는 Node.js환경)에서 추가로 제공하는 객체

브라우저-DOM,BOM,Canvas, XMLHttpRequest,fetch,requestAnimationFrame,SVG,WebStorage,WebComponent,WebWorker와 같은 클라이언트 사이드 Web API호스트 객체로 제공

Node.js환경- Node.js고유의 API 호스트 객체로 제공

### 3.  사용자 정의 객체

## 21.2 표준 빌트인 객체

자바스크립트는 object, String, Number,… ,Symbol, Date, Math, RegExp, Array, Map/Set, Set, WeakMap/WeakSet, Function, Promise, Reflect, Proxy, JSON, Error 등 40여개의 표준 빌트인 객체를 제공

Math, Reflect, JSON을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 생상자 함수 객체

생성자 함수 객체인 표준 빌트인 객체는 프로토타입 메서드오 ㅏ정적 메서드를 제공하고 생상저 함수객체가 아닌 표준 빌트인 객체는 정적 메서드만 제공

```jsx
const strObj=new String("Lee")//String{"lee"}
console.log(typeof strObj)//object
console.log(Object.getPrototypeOf(strObj)===String.prototype);//true
```

생성자 함수인 표준 빌트인 객체가 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 prototype프로퍼티에 바인딩된 객체

표준 빌트인 객체의 prototype 프로퍼티에 바인딩 된 객체는 다양한 기능의 빌트인 프로토타입 메서드를 제공, 표준 빌트인 객체는 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공

```jsx
const numObj=new Number(1.5);
//Number.prototype.toFixed는 소수점 자리를 반올림하여 문자열로 반환
console.log(numObj.toFixed()); //2
//isInteger은 Number의 정적 메서드
console.log(Number.isInteger(0.5));//false
```

그럼 원시값은 메서드가? 당연히 됨

```jsx
const num=1.5
console.log(num.toFixed());//2
console.log(Number.isInteger(0.5));//false
```

## 21.3 원시값과 래퍼 객체

문자열이나 숫자, 불리언 등의 원시값이 있는데도, 문자열, 숫자, 불리언 객체를 생성하는 표준 빌트인 객체가 왜 존재하는 걸까? 방금 위에서 원시값은 메서드가 당연히 됨이라는 의문에서 이어진다고 불 수 있었음..(궁금한거 바로 해결~)

```jsx
const str="hello";
console.log(str.length)//5
console.log(str.toUpperCase());//HELLO
```

원시값인 문자열, 수수자, 불리언 값의 경우 이들 원시값에 대해 마치 객체처럼 마침표 표기법(또는 대괄호 표기법)으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해주기 때문

즉, 원시값을 객체처럼 사용하면 자바스크립트 엔진은 암묵적으로 연관된 객체를 생성하여 생성된 객체로 프로퍼티에 접근하거나 메서드를 호출하고 디시 원시값으로 되돌린다.

`래퍼객체(wapper object)` : 문자열, 숫자, 불리언 값에 대해 객체처럼 접근하면 되는 임시 객체

ex) 문자열에 대해 마침표 표기법으로 접근하면 그 순간 래퍼 객체인 String 생성자 함수의 인스턴스가 생성→ 문자열은 래퍼 객체의 [[StringData]] 내부 슬롯에 할당→ 래퍼 객체의 처리가 종료되면 래퍼 객체의 [[StringData]]내부 슬롯에 할당된 원시값으로 원래 상태, 즉 식별자가 우너시값을 갖도록 되돌리고 래퍼 객체는 가비지 컬렉션의 대상이 됨

이때, 문자열 래퍼 객체인 String 생성자 함수의 인스턴스는 String.prototype의 메서드를 상속받아 사용할 수 있다.

```jsx
const str="hello"
str.name="lee" //'lee'
console.log(str.name) //undefiend
```

⇒ 문자열, 숫자, 불리언, 심벌은 암묵적으로 생성되는 래퍼객체에 의해 마치 객체처럼 사용할 수 있으며, 표준 빌트인 객체인 String, Number, Boolean, Symbol의 프로토타입 메서드 또는 프로퍼티를 참조할 수 있다. 

⇒ String, Number, Boolean 생성자 함수를 new 연산자와 함께 호출하여 문자열, 숫자, 불리언 인스턴스를 생성할 필요가 없으며 권장하지도 않음.. 

null, undefined는 래퍼객체를 생성하지 않음!(객체처럼 사용하면 당연히 에러)

## 21.4 전역 객체

코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체, 어떤 객체에도 속하지 않은 최상위 객체

전역 객체는 자바스크립트 환경에 따라 지칭하는 이름이 제각각임

브라우저 환경에서는 window(또는 self, this, frames) 

Node.js환경에서는 global이 전역 객체

전역 객체는 표준 빌트인 객체(Object, String, Number, Function, Array 등)와 환경에 따른 호스트 객체(클라이언트 Web API 또는 Node.js의 호스트 API), 그리고 var키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 가짐.

- 전역 객체는 개발자가 의도적으로 생성할 수 없다. 즉, 전역 객체를 생성할 수 있는 생성자 함수가 제공되지 않는다.
- 전역 객체의 프로퍼티를 참조할 때 window(또는 global)를 생략할 수 있다.
- 전역 객체는 Object, String, Number, Boolean, Function, Arrray, RegExp, Date, Math, Promise 같은 모든 표준 빌트인 객체를 프로퍼티로 가지고 있다
- 자바스크립트 실행환경(브라우저 환경 또는 Node.js환경)에 따라 추가적으로 프로퍼티와 메서드를 갖는다
    - 브라우저 환경에서는 클라이언트 사이드 WebAPI(DOM,BOM,Canvas,XmlHttpRequest, fetch, requestAnimationFrame, SVG,Web Component, WebWorker)
    - Node.js고유의 API
- var키워드로 선언한 전역변수와 선언하지 않은 벼눗에 값을 할당한 암묵적 전역 그리고 전역 함수는 전역 객체의 프로퍼티가 됨
    - let, const키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.
- 브라우저 환경의 모든 자바스크립트 코드는 하나의 전역 객체 window를 공유한다. 여러개의 script태그를 통해 자바스크립트 코드를 분리해도 하나의 전역 객체 window를 공유하는 것은 변함 없음. → 분리되어 있는 자바스크립트 코드가 하나의 전역을 공유한다.

### 21.4.1 빌트인 전역 프로퍼티

`Infinity` 

`NaN`

`undefiend` 

### 21.4.2 빌트인 전역 함수

`eval` ⇒ 🚫금지🚫

eval 함수는 자바스크립트 코드를 나타내는 문자열을 인수로 전달 받음

전달받은 문자열 코드가 표혀닉이 아니라면 eval 함수는 문자열 코드를 런타임에 평가하여 값을 생성하고 전달 받은 인수가 표현식이 아닌 문이라면 eval 함수는 문자열 코드를 런타임에 실행. 

문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행

인수로 전달받은 문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행한 다음 마지막 결과값을 반환

```jsx
eval('1+2; 3+4;');//7
```

eval 함수는 기존의 스코프를 런타임에 동적으로 수정

```jsx
const x=1;
function foo(){
eval('val x=2;');
console.log(x);//2
}
foo();
console.log(x);//1
```

단, strict mode에서 eval 함수는 기존의 스코프를 수정하지 않고 eval 함수 자신의 자체적인 스코프를 생성

또한, 인수로 전달받은 문자열 코드가 let, const 키워드를 사용한 변수 선언문이라면 암묵적으로 strict mode가 적용됨

`isFinite`

전달 받은 인수가 정상적인 유한수인지 검사→ 유한수이면 true, 무한수이면 false(NaN도)

`isNaN` 

전달받은 인수가 NaN인지 검사하여 불리언 타입으로 반환

전달받은 인수의 타입이 숫자가 아닌 경우 숫자로 타입을 변환한 후 검사를 수행

`parseFloat`

```jsx
/**
* 전달 받은 문자열 인수를 실수로 해석하여 반환
* @param {string} string - 변환 대상 값
* @returns {number}  변환 결과
*/
parseFloat(stirng)
parseFloat('3.14')
//3.14
//공백으로 구분된 문자열은 첫번째 문자열만 변환
parseFloat('34 45 66')
//34
parseFloat('40 years')
//40
parseFloat('10.00')
//10
//앞뒤 공백은 무시
parseFloat(' 60 ')
//60
```

`parseInt`

전달 받은 문자열 인수를 정수로 해석하여 변환

```jsx
/**
* 전달받은 문자열 인수를 정수로 해석하여 반환
* @param {string} string - 변환 대상 값
* @param {number} [radix] - 진법을 나타내는 기수(2~36, 기본값 10)
* @returns {number} 변환 결과
*/
parseInt(string, radix);
parseInt('10')//10
//전달받은 인수가 문자열이 아니면 문자열로 변환 후 정수로 해석하여 반환
parseInt('10.123')//10
```

cf) 기수를 지정하여 10진수 숫자를 해당 기수의 문자열로 변화하여 반환하고 싶을 때는 Number.prototype.toString메서드를 사용

두 번째 인수로 진법을 나타내는 기수를 지정하지 않더라도 첫번째 인수로 전달된 문자열이 “0x”또는 0X”로 시작하는 16진수 리터럴이면 16진수로 해석하여 10진수 정수로 반환(2,8진수 리터럴은 제대로 해석하지 못함)

`encodeURI`/ `decodeURI`  

encodeURI함수는 완전한 URI를 문자열로 전달받아 이스케이프처리를 위해 인코딩을 함

URI 문법 형식 표준에 따라 URL은 아스키 문자 셋으로만 구성되어야 함. 한글을 포함한 대부분의 외구구어나 아스키 문자 셋에 정의되어 있지 않은 특수문자의 경우 URL에 포함될 수 없다. 따라서 URL 내에서 의미를 갖고 있는 문자(%,?,#)나 URL에 올 수 없는 (한글, 공백 등) 또는 시스템에 의해 해석될 수 있는 문자(<,>)를 이스케이프 처리하여 야기될 수 있는 문제를 예방하기 위해 이스케이프 처리가 필요

단, 알파벳, 0~9숫자, -_.!~*’()문자는 이스케이프 처리에서 제외

```jsx
const uri="http://example.com?name=김세은&job=programmer&teacher";
const enc=encodeURI(uri)
console.log(enc)
//http://example.com?name=%EA%B9%80%EC%84%B8%EC%9D%80&job=programmer&teacher
console.log(decodeURI(enc))
//http://example.com?name=김세은&job=programmer&teacher
```

`encodeURIComponent`/ `decodeURIComponent`

encodeURIComponent함수는 URI 구성 요소(component)를 인수로 전달받아 인코딩

인수로 전달된 문자열을 URI의 구성 요소인 쿼리스트링의 일부로 간주. 따라서 쿼리 스트링 구분자로 사용되는 =,?,&까지 인코딩

encodeURI는 매개변수로 전달된 문자열을 완전한 URI전체라고 간주⇒쿼리 스트링 구분자로 사용되는 =,?,&은 인코딩 하지 않음.

```jsx
const uriComp='name=김세은&job=programmer&teacher'
let enc=encodeURIComponent(uriComp)
console.log(enc) 
//name%3D%EA%B9%80%EC%84%B8%EC%9D%80%26job%3Dprogrammer%26teacher
let dec=decodeURIComponent(enc)
console.log(dec)
//name=김세은&job=programmer&teacher
enc=encodeURI(uriComp)
//'name=%EA%B9%80%EC%84%B8%EC%9D%80&job=programmer&teacher'
dec=decodeURI(enc)
//'name=김세은&job=programmer&teacher'
```

### 21.4.3 암묵적 전역

전역 객체의 프로퍼티가 되어 마치 전역변수처럼 동작하는 현상

변수 호이스팅이 발생하지는 않음

```jsx
var x=10
function foo(){
    y=20//window.y=20
}
foo()
console.log(x+y)//30
delete x; //false
delete y; //true
```

변수가 아니라 프로퍼티인 y는 delete연산자로 삭제 가능

전역 변수는 프로퍼티이지만 delete연산자로 삭제할 수 없음# 21장 빌트인 객체

## 21.1 자바스크립트 객체의 분류

### 1. 표준 빌트인 객체

ECMAScript 사양에 정의된 객체→ 실행 환경과 관계없이 언제나 사용 가능

애플리케이션 전역의 공통 기능을 제공

별도의 선언 없이 전역 변수 처럼 언제나 참조될 수 있음

### 2. 호스트 객체

ECMAScript 사양에 정의되어 있지는 않지만 자바스크립트 실행환경(브라우저 환경 또는 Node.js환경)에서 추가로 제공하는 객체

브라우저-DOM,BOM,Canvas, XMLHttpRequest,fetch,requestAnimationFrame,SVG,WebStorage,WebComponent,WebWorker와 같은 클라이언트 사이드 Web API호스트 객체로 제공

Node.js환경- Node.js고유의 API 호스트 객체로 제공

### 3.  사용자 정의 객체

## 21.2 표준 빌트인 객체

자바스크립트는 object, String, Number,… ,Symbol, Date, Math, RegExp, Array, Map/Set, Set, WeakMap/WeakSet, Function, Promise, Reflect, Proxy, JSON, Error 등 40여개의 표준 빌트인 객체를 제공

Math, Reflect, JSON을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 생상자 함수 객체

생성자 함수 객체인 표준 빌트인 객체는 프로토타입 메서드오 ㅏ정적 메서드를 제공하고 생상저 함수객체가 아닌 표준 빌트인 객체는 정적 메서드만 제공

```jsx
const strObj=new String("Lee")//String{"lee"}
console.log(typeof strObj)//object
console.log(Object.getPrototypeOf(strObj)===String.prototype);//true
```

생성자 함수인 표준 빌트인 객체가 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 prototype프로퍼티에 바인딩된 객체

표준 빌트인 객체의 prototype 프로퍼티에 바인딩 된 객체는 다양한 기능의 빌트인 프로토타입 메서드를 제공, 표준 빌트인 객체는 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공

```jsx
const numObj=new Number(1.5);
//Number.prototype.toFixed는 소수점 자리를 반올림하여 문자열로 반환
console.log(numObj.toFixed()); //2
//isInteger은 Number의 정적 메서드
console.log(Number.isInteger(0.5));//false
```

그럼 원시값은 메서드가? 당연히 됨

```jsx
const num=1.5
console.log(num.toFixed());//2
console.log(Number.isInteger(0.5));//false
```

## 21.3 원시값과 래퍼 객체

문자열이나 숫자, 불리언 등의 원시값이 있는데도, 문자열, 숫자, 불리언 객체를 생성하는 표준 빌트인 객체가 왜 존재하는 걸까? 방금 위에서 원시값은 메서드가 당연히 됨이라는 의문에서 이어진다고 불 수 있었음..(궁금한거 바로 해결~)

```jsx
const str="hello";
console.log(str.length)//5
console.log(str.toUpperCase());//HELLO
```

원시값인 문자열, 수수자, 불리언 값의 경우 이들 원시값에 대해 마치 객체처럼 마침표 표기법(또는 대괄호 표기법)으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해주기 때문

즉, 원시값을 객체처럼 사용하면 자바스크립트 엔진은 암묵적으로 연관된 객체를 생성하여 생성된 객체로 프로퍼티에 접근하거나 메서드를 호출하고 디시 원시값으로 되돌린다.

`래퍼객체(wapper object)` : 문자열, 숫자, 불리언 값에 대해 객체처럼 접근하면 되는 임시 객체

ex) 문자열에 대해 마침표 표기법으로 접근하면 그 순간 래퍼 객체인 String 생성자 함수의 인스턴스가 생성→ 문자열은 래퍼 객체의 [[StringData]] 내부 슬롯에 할당→ 래퍼 객체의 처리가 종료되면 래퍼 객체의 [[StringData]]내부 슬롯에 할당된 원시값으로 원래 상태, 즉 식별자가 우너시값을 갖도록 되돌리고 래퍼 객체는 가비지 컬렉션의 대상이 됨

이때, 문자열 래퍼 객체인 String 생성자 함수의 인스턴스는 String.prototype의 메서드를 상속받아 사용할 수 있다.

```jsx
const str="hello"
str.name="lee" //'lee'
console.log(str.name) //undefiend
```

⇒ 문자열, 숫자, 불리언, 심벌은 암묵적으로 생성되는 래퍼객체에 의해 마치 객체처럼 사용할 수 있으며, 표준 빌트인 객체인 String, Number, Boolean, Symbol의 프로토타입 메서드 또는 프로퍼티를 참조할 수 있다. 

⇒ String, Number, Boolean 생성자 함수를 new 연산자와 함께 호출하여 문자열, 숫자, 불리언 인스턴스를 생성할 필요가 없으며 권장하지도 않음.. 

null, undefined는 래퍼객체를 생성하지 않음!(객체처럼 사용하면 당연히 에러)

## 21.4 전역 객체

코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체, 어떤 객체에도 속하지 않은 최상위 객체

전역 객체는 자바스크립트 환경에 따라 지칭하는 이름이 제각각임

브라우저 환경에서는 window(또는 self, this, frames) 

Node.js환경에서는 global이 전역 객체

전역 객체는 표준 빌트인 객체(Object, String, Number, Function, Array 등)와 환경에 따른 호스트 객체(클라이언트 Web API 또는 Node.js의 호스트 API), 그리고 var키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 가짐.

- 전역 객체는 개발자가 의도적으로 생성할 수 없다. 즉, 전역 객체를 생성할 수 있는 생성자 함수가 제공되지 않는다.
- 전역 객체의 프로퍼티를 참조할 때 window(또는 global)를 생략할 수 있다.
- 전역 객체는 Object, String, Number, Boolean, Function, Arrray, RegExp, Date, Math, Promise 같은 모든 표준 빌트인 객체를 프로퍼티로 가지고 있다
- 자바스크립트 실행환경(브라우저 환경 또는 Node.js환경)에 따라 추가적으로 프로퍼티와 메서드를 갖는다
    - 브라우저 환경에서는 클라이언트 사이드 WebAPI(DOM,BOM,Canvas,XmlHttpRequest, fetch, requestAnimationFrame, SVG,Web Component, WebWorker)
    - Node.js고유의 API
- var키워드로 선언한 전역변수와 선언하지 않은 벼눗에 값을 할당한 암묵적 전역 그리고 전역 함수는 전역 객체의 프로퍼티가 됨
    - let, const키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.
- 브라우저 환경의 모든 자바스크립트 코드는 하나의 전역 객체 window를 공유한다. 여러개의 script태그를 통해 자바스크립트 코드를 분리해도 하나의 전역 객체 window를 공유하는 것은 변함 없음. → 분리되어 있는 자바스크립트 코드가 하나의 전역을 공유한다.

### 21.4.1 빌트인 전역 프로퍼티

`Infinity` 

`NaN`

`undefiend` 

### 21.4.2 빌트인 전역 함수

`eval` ⇒ 🚫금지🚫

eval 함수는 자바스크립트 코드를 나타내는 문자열을 인수로 전달 받음

전달받은 문자열 코드가 표혀닉이 아니라면 eval 함수는 문자열 코드를 런타임에 평가하여 값을 생성하고 전달 받은 인수가 표현식이 아닌 문이라면 eval 함수는 문자열 코드를 런타임에 실행. 

문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행

인수로 전달받은 문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행한 다음 마지막 결과값을 반환

```jsx
eval('1+2; 3+4;');//7
```

eval 함수는 기존의 스코프를 런타임에 동적으로 수정

```jsx
const x=1;
function foo(){
eval('val x=2;');
console.log(x);//2
}
foo();
console.log(x);//1
```

단, strict mode에서 eval 함수는 기존의 스코프를 수정하지 않고 eval 함수 자신의 자체적인 스코프를 생성

또한, 인수로 전달받은 문자열 코드가 let, const 키워드를 사용한 변수 선언문이라면 암묵적으로 strict mode가 적용됨

`isFinite`

전달 받은 인수가 정상적인 유한수인지 검사→ 유한수이면 true, 무한수이면 false(NaN도)

`isNaN` 

전달받은 인수가 NaN인지 검사하여 불리언 타입으로 반환

전달받은 인수의 타입이 숫자가 아닌 경우 숫자로 타입을 변환한 후 검사를 수행

`parseFloat`

```jsx
/**
* 전달 받은 문자열 인수를 실수로 해석하여 반환
* @param {string} string - 변환 대상 값
* @returns {number}  변환 결과
*/
parseFloat(stirng)
parseFloat('3.14')
//3.14
//공백으로 구분된 문자열은 첫번째 문자열만 변환
parseFloat('34 45 66')
//34
parseFloat('40 years')
//40
parseFloat('10.00')
//10
//앞뒤 공백은 무시
parseFloat(' 60 ')
//60
```

`parseInt`

전달 받은 문자열 인수를 정수로 해석하여 변환

```jsx
/**
* 전달받은 문자열 인수를 정수로 해석하여 반환
* @param {string} string - 변환 대상 값
* @param {number} [radix] - 진법을 나타내는 기수(2~36, 기본값 10)
* @returns {number} 변환 결과
*/
parseInt(string, radix);
parseInt('10')//10
//전달받은 인수가 문자열이 아니면 문자열로 변환 후 정수로 해석하여 반환
parseInt('10.123')//10
```

cf) 기수를 지정하여 10진수 숫자를 해당 기수의 문자열로 변화하여 반환하고 싶을 때는 Number.prototype.toString메서드를 사용

두 번째 인수로 진법을 나타내는 기수를 지정하지 않더라도 첫번째 인수로 전달된 문자열이 “0x”또는 0X”로 시작하는 16진수 리터럴이면 16진수로 해석하여 10진수 정수로 반환(2,8진수 리터럴은 제대로 해석하지 못함)

`encodeURI`/ `decodeURI`  

encodeURI함수는 완전한 URI를 문자열로 전달받아 이스케이프처리를 위해 인코딩을 함

URI 문법 형식 표준에 따라 URL은 아스키 문자 셋으로만 구성되어야 함. 한글을 포함한 대부분의 외구구어나 아스키 문자 셋에 정의되어 있지 않은 특수문자의 경우 URL에 포함될 수 없다. 따라서 URL 내에서 의미를 갖고 있는 문자(%,?,#)나 URL에 올 수 없는 (한글, 공백 등) 또는 시스템에 의해 해석될 수 있는 문자(<,>)를 이스케이프 처리하여 야기될 수 있는 문제를 예방하기 위해 이스케이프 처리가 필요

단, 알파벳, 0~9숫자, -_.!~*’()문자는 이스케이프 처리에서 제외

```jsx
const uri="http://example.com?name=김세은&job=programmer&teacher";
const enc=encodeURI(uri)
console.log(enc)
//http://example.com?name=%EA%B9%80%EC%84%B8%EC%9D%80&job=programmer&teacher
console.log(decodeURI(enc))
//http://example.com?name=김세은&job=programmer&teacher
```

`encodeURIComponent`/ `decodeURIComponent`

encodeURIComponent함수는 URI 구성 요소(component)를 인수로 전달받아 인코딩

인수로 전달된 문자열을 URI의 구성 요소인 쿼리스트링의 일부로 간주. 따라서 쿼리 스트링 구분자로 사용되는 =,?,&까지 인코딩

encodeURI는 매개변수로 전달된 문자열을 완전한 URI전체라고 간주⇒쿼리 스트링 구분자로 사용되는 =,?,&은 인코딩 하지 않음.

```jsx
const uriComp='name=김세은&job=programmer&teacher'
let enc=encodeURIComponent(uriComp)
console.log(enc) 
//name%3D%EA%B9%80%EC%84%B8%EC%9D%80%26job%3Dprogrammer%26teacher
let dec=decodeURIComponent(enc)
console.log(dec)
//name=김세은&job=programmer&teacher
enc=encodeURI(uriComp)
//'name=%EA%B9%80%EC%84%B8%EC%9D%80&job=programmer&teacher'
dec=decodeURI(enc)
//'name=김세은&job=programmer&teacher'
```

### 21.4.3 암묵적 전역

전역 객체의 프로퍼티가 되어 마치 전역변수처럼 동작하는 현상

변수 호이스팅이 발생하지는 않음

```jsx
var x=10
function foo(){
    y=20//window.y=20
}
foo()
console.log(x+y)//30
delete x; //false
delete y; //true
```

변수가 아니라 프로퍼티인 y는 delete연산자로 삭제 가능

전역 변수는 프로퍼티이지만 delete연산자로 삭제할 수 없음