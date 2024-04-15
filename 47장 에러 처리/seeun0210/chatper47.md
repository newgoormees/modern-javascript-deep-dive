# 에러 처리

## 47.1 에러 처리의 필요성

에러(error)가 발생하지 않는 코드를 작성하는 것은 불가능 한데, 발생한 에러를 방치하면 프로그램을 강제 종료된다. 이는 try...catch 문을 사용해 적절하게 대응할 수 있다.

```javascript
console.log('[Start]');

try {
    foo();
} catch (error) {
    console.error('[에러 발생]', error);
    // [에러 발생] ReferenceError: foo is not defined
}
console.log('[End]'); // 적절한 대응으로 강제 종료 방지
```

------

직접적으로 에러를 발생하지 않는 예외(exception)적인 상황이 발생할 수도 있다. 이 경우도 적절히 대응하지 않으면 에러로 이어질 수 있다.

예제를 살펴보자.

```javascript
// DOM에 button 요소가 존재하지 않으면 querySelector 메서드는 에러가 아닌 null을 반환
const $button = document.querySelector('button'); // null

$button.classList.add('disabled');
// TypeError: Cannot read property 'classList' of null
```

이 때, if 문으로 querySelector 메서드의 반환값을 확인하거나, 단축 평가를 하거나, 옵셔널 체이닝 연산자 ?.를 사용해야 한다.

```javascript
const $button = document.querySelector('button'); //null

$button?.classList.add('disabled');
```

에러나 예외적인 상황은 너무 다양하여 대응하기 어렵지만, 언제나 발생하는 것이라는 것을 전제하고 대응하는 코드를 작성할 수 있어야한다.

------

## 47.2 try...catch...finally 문

기본적으로 에러 처리 하는 방법은 두 가지이다.

1. querySelector나 Array#find 메서드처럼 예외적인 상황이 발생하면 반환되는 값(null 또는 -1)을 if 문이나 단축 평가, 옵셔널 체이닝 연산자를 통해 확인해서 처리하는 방법

2. 에러 처리 코드를 미리 등록해 두고 에러가 발생하면 에러 처리 코드로 점프하도록 하는 방법
`try...catch...finally`는 두 번째 방법으로, 이 방법을 일반적으로 에러 처리(error handling)이라고 한다.

```javascript
try {
    // 실행할 코드 (에러 발생 가능성 있는 코드)
} catch (err) {
    // try 코드 블록에서 에러 발생 시 이 코드가 실행된다.
    // err 에는 try 코드 블록에서 발생한 Error 객체가 전달되며, catch 코드 블록에서만 유효하다.
} finally {
    // 에러 발생과 상관 없이 반드시 한 번 실행되며, 생략 가능하다.
}
```

------

## 47.3 Error 객체

Error 생성자 함수가 에러 객체를 생성하며, 에러를 상세히 설명하는 에러 메세지를 인수로 전달한다.
에러 객체는 message, stack 프로퍼티를 갖는다.
message 프로퍼티의 값은 에러 메세지, stack 프로퍼티의 값은 에러를 발생시킨 콜스택의 호출 정보를 나타내는 문자열이며, 디버깅 목적으로 사용된다.
자바스크립트는 Error 생성자 함수 7가지를 제공한다. 이 함수들이 생성한 에러 객체의 프로토타입은 모두 Error.prototype 을 상속받는다.
|생성자 함수|인스턴스|
|------|------|
|Error|일반적 에러 객체|
|SyntaxError|자바스크립트 문법에 맞지 않는 문을 해석할 때 발생하는 에러 객체|
|ReferenceError|참조할 수 없는 식별자를 참조했을 때 발생하는 에러 객체|
|TypeError|피연산자 또는 인수의 데이터 타입이 유효하지 않을 때 발생하는 에러 객체|
|RangeError|숫자값의 허용 범위를 벗어났을 때 발생하는 에러 객체|
|URIError|encodeURI 또는 decodeURI 함수에 부적절한 인수를 전달했을 때 발생하는 에러 객체|
|EvalError|eval 함수에서 발생하는 에러 객체|
------

## 47.4 throw 문

에러 객체 생성과 에러 발생은 의미를 구별할 필요가 있다.

```javascript
try {
    new Error('something wrong'); // 에러 객체 생성, 에러 발생되진 않음
} catch (error) {
    console.log(error);
}
```

에러를 발생시키려면 try 코드 블록에서 throw 문으로 에러 객체를 던져야 한다. 에러를 던지면 에러 변수가 생성되고 던져진 에러 객체가 할당된다. 그리고 catch 코드 블록이 실행되기 시작한다.

```javascript
try {
    throw new Error('something wrong');
} catch (error) {
    console.log(error);
}
```

예제로 확인해보자.

```javascript
// 외부에서 전달받은 콜백 함수를 n번 만큼 반복 호출한다.
const repeat = (n, f) => {
    if (typeof f !== 'function') throw new TypeError('f must be a function');

    for (var i = 0; i < n; i++ ) {
        f(i); // i를 전달하면서 f를 호출
    }
};

try {
    repeat(2, 1); // 두 번째 인수가 함수가 아니므로 TypeError가 발생(throw)한다.
} catch (err) {
    console.error(err); //TypeError: f must be a function
}

```

------
## 47.5 에러의 전파

45장 "프로미스", 45.1.2절 "에러 처리의 한계"에서 본 바와 같이, 에러는 호출자 방향으로 전파된다. 즉, 콜 스택의 아래 방향으로 전파된다.

```javascript
const foo = () => {
    throw Error('foo에서 발생한 에러'); // D
};

const bar = () => {
    foo(); // C
};

const baz = () => {
    bar(); // B
};

try {
    baz(); // A
} catch(err) {
    console.error(err);
}
```

A에서 baz 함수를 호출하면, B에서 bar 함수가 호출되고, C에서 foo 함수가 호출되고, foo 함수는 D에서 에러를 throw 한다. 이때 foo 함수가 throw 한 에러는 호출자에게 전파되어 전역에서 캐치된다.
이처럼 throw 된 에러를 캐치하여 적절히 대응해야 코드 실행 흐름을 복구할 수 있다. 어디에서도 캐치하지 않으면 프로그램은 강제 종료된다.

- 주의해야 할 것 : 비동기 함수인 setTimeout 이나 프로미스 후속 처리 메서드의 콜백 함수는 호출자가 없다.
  
  이들은 태스크 큐나 마이크로태스크 큐에 일시 저장되었다가, 콜 스택이 비면 이벤트 루프에 의해 콜 스팩으로 푸시된다. 이 때 푸시된 콜백 함수의 실행 컨텍스트는 콜 스택의 가장 하부에 존재하게 된다. 따라서 에러를 전파할 호출자가 존재하지 않는다.

## 추가-에러 꼬리물기(Error Cause)

- 에러 꼬리물기는 자바스크립트에서 에러 처리를 개선하기 위해 ECMAScript 2022에 도입된 기능입니다. 이 기능은 기존의 에러 객체에 "cause" 속성을 추가하여, 에러가 발생한 원인을 명확하게 설명할 수 있도록 도움.

- 이를 통해 개발자는 에러의 연쇄적인 원인을 추적하고 디버깅을 보다 효율적으로 수행할 수 있음.

### 동작 방식

`Error` 객체와 그 서브클래스(예: `RangeError`, `TypeError` 등)는 이제 "cause" 옵션을 받을 수 있는 생성자를 가집니다. 이 옵션을 사용하여 발생한 예외의 근본 원인을 나타내는 오브젝트를 전달할 수 있습니다.

ex)

```javascript
try {
  // 에러를 발생시키는 함수 호출
  functionThatMightThrow();
} catch (error) {
  throw new Error("상위 수준의 에러 메시지", { cause: error });
}
```

functionThatMightThrow() 함수가 실패할 경우, 해당 에러를 잡아 새로운 Error 객체를 생성하면서 원인 에러를 cause 속성에 전달합니다. 이로써 상위 수준에서 발생한 에러와 원인이 된 하위 수준의 에러를 연결할 수 있음.