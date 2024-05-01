# 46장. 제러레이터와 async/await

## 목차

- [46.1 제너레이터란?](#461-제너레이터란)
  * [제너레이터와 일반 함수의 차이](#제너레이터와-일반-함수의-차이)
- [46.2 제너레이터 함수의 정의](#462-제너레이터-함수의-정의)
- [46.3 제너레이터 객체](#463-제너레이터-객체)
  * [제너레이터 객체의 동작](#제너레이터-객체의-동작)
- [46.4 제너레이터의 일시 중지와 재개](#464-제너레이터의-일시-중지와-재개)
  * [예시 1](#예시-1)
  * [예시 2](#예시-2)
- [46.5 제너레이터의 활용](#465-제너레이터의-활용)
  * [46.5.1 이터러블의 구현](#4651-이터러블의-구현)
  * [46.5.2 비동기 처리](#4652-비동기-처리)
- [46.6 async/await](#466-asyncawait)
  * [46.6.1 async 함수](#4661-async-함수)
  * [46.6.2 await 키워드](#4662-await-키워드)
  * [46.6.3 에러 처리](#4663-에러-처리)

---

## 46.1 제너레이터란?

ES6에서 도입된 제너레이터는 코드 블록의 실행을 **일시 중지**했다가 필요한 시점에 **재개**할 수 있는 특수한 함수다.

### 제너레이터와 일반 함수의 차이

1. 제너레이터 함수는 함수 호출자에게 **함수 실행의 제어권을 양도**할 수 있다.
2. 제너레이터 함수는 함수 호출자와 **함수의 상태**를 주고받을 수 있다.
3. 제너레이터 함수를 호출하면 **제너레이터 객체**를 반환한다.
    - 제너레이터 객체는 이터러블이면서 동시에 이터레이터이다.

## 46.2 제너레이터 함수의 정의

제너레이터 함수는 `function*` 키워드로 선언한다. 그리고 하나 이상의 `yield` 표현식을 포함한다.

이 점을 제외하면, 일반 함수를 정의하는 방법과 같다.

```jsx
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  * genObjMethod() {
    yield 1;
  }
};

// 제너레이터 클래스 메서드
class MyClass {
  * genClsMethod() {
    yield 1;
  }
}
```

제너레이터 함수는 화살표 함수로 정의하거나, new 연산자와 함께 생성자 함수로 호출할 수 없다.

## 46.3 제너레이터 객체

제너레이터 함수를 호출하면 **이터러블**이면서 동시에 **이터레이터**인 **제너레이터 객체**를 생성해 반환한다.

- `Symbol.iterator` 메서드를 상속받음
- `next` 메서드를 소유함
    - `value`, `done` 프로퍼티를 갖는 **이터레이터 리절트 객체**를 반환
- `return`, `throw` 메서드를 가짐
    - 이터레이터에는 없는 메서드

### 제너레이터 객체의 동작

제너레이터의 객체가 가지는 세 개의 메서드를 호출했을 때 다음과 같은 동작을 한다.

- `next` 메서드 호출 시, 제너레이터 함수의 `yield` 표현식까지 코드 블록을 실행하고, `yield` 된 값을 `value` 프로퍼티 값으로, `false`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
- `return` 메서드 호출 시, 인자로 전달받은 값을 `value` 프로퍼티 값으로, `true`를 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

```jsx
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.return('End!')); // {value: "End!", done: true}
```

- `throw` 메서드 호출 시, 인수로 전달받은 에러를 발생시키고, `undefined`를 `value` 프로퍼티 값으로, `true`를 `done` 프로피터 값으로 갖는 이터레이터 리절트 객체를 반환한다.

```jsx
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.throw('Error!')); // {value: undefined, done: true}
```

## 46.4 제너레이터의 일시 중지와 재개

제너레이터 객체의 `next` 메서드를 호출하면 `yield` 표현식까지 실행되고 일시 중지된다. 이 때 함수의 제어권이 호출자로 양도되기 때문에  `yield` 키워드와 `next` 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개할 수 있다. 

`next` 메서드는 `{value, done}`의 이터레이터 리절트 객체를 반환한다.

### 예시 1

```jsx
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc();

// 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
// value 프로퍼티에는 첫 번째 yield 표현식에서 **yield된 값** 1이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 1, done: false}

// 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
// value 프로퍼티에는 두 번째 yield 표현식에서 yield된 값 2가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 2, done: false}

// 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 실행되고 일시 중지된다.
// value 프로퍼티에는 세 번째 yield 표현식에서 **yield된 값** 3이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 3, done: false}

// 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행한다.
// value 프로퍼티에는 **제너레이터 함수의 반환값** undefined가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었음을 나타내는 true가 할당된다.
console.log(generator.next()); // {value: undefined, done: true}
```

다음 코드에서 주목해야 할 점은 다음과 같다.

- `value` 프로퍼티에는 `yield` 된 값이 할당된다.
- `done` 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 `boolean` 값이 할당된다.
- 마지막으로 호출되는 `next` 메서드의 `value` 프로퍼티는 제너레이터 함수의 **반환값**이 할당된다.

### 예시 2

제너레이터 객체의 `next` 메서드에는 **인수를 전달**할 수 있다.

전달된 인수는 제너레이터 함수의 `yield` 표현식을 할당받는 변수에 할당된다.

```jsx
function* genFunc() {
  // 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 1은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // x 변수에는 아직 아무것도 할당되지 않았다. **x 변수의 값은 next 메서드가 두 번째 호출될 때 결정**된다.
  const x = yield 1;

  // 두 번째 next 메서드를 호출할 때 전달한 **인수 10은 첫 번째 yield 표현식을 할당받는 x 변수에 할당**된다.
  // 즉, const x = yield 1;은 두 번째 next 메서드를 호출했을 때 완료된다.
  // 두 번째 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 x + 10은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  const y = yield (x + 10);

  // 세 번째 next 메서드를 호출할 때 전달한 **인수 20은 두 번째 yield 표현식을 할당받는 y 변수에 할당**된다.
  // 즉, const y = yield (x + 10);는 세 번째 next 메서드를 호출했을 때 완료된다.
  // 세 번째 next 메서드를 호출하면 함수 끝까지 실행된다.
  // 이때 제너레이터 함수의 반환값 x + y는 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // 일반적으로 제너레이터의 반환값은 의미가 없다.
  // 따라서 제너레이터에서는 값을 반환할 필요가 없고 return은 종료의 의미로만 사용해야 한다.
  return x + y;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이며 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc(0);

// **처음 호출하는 next 메서드에는 인수를 전달하지 않는다**.
// 만약 처음 호출하는 next 메서드에 인수를 전달하면 무시된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 첫 번째 yield된 값 1이 할당된다.
let res = generator.next();
console.log(res); // {value: 1, done: false}

// next 메서드에 인수로 전달한 10은 genFunc 함수의 x 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 두 번째 yield된 값 20이 할당된다.
res = generator.next(10);
console.log(res); // {value: 20, done: false}

// next 메서드에 인수로 전달한 20은 genFunc 함수의 y 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값 30이 할당된다.
res = generator.next(20);
console.log(res); // {value: 30, done: true}
```

다음 코드에서 주목해야 할 점은 다음과 같다.

- 처음 호출하는 `next` 메서드의 인수는 무시된다.
- `next` 메서드에 인수를 넣게 되면, 그 값은 이전 `yield` 표현식을 할당받는 변수에 할당되게 된다.

## 46.5 제너레이터의 활용

### 46.5.1 이터러블의 구현

제너레이터 함수를 사용하면 간단히 이터러블을 구현할 수 있다.

- 이터레이션 프로토콜을 준수하여 생성 시
    
    ```jsx
    // 무한 이터러블을 생성하는 함수
    const infiniteFibonacci = (function () {
      let [pre, cur] = [0, 1];
    
      return {
        [Symbol.iterator]() { return this; },
        next() {
          [pre, cur] = [cur, pre + cur];
          // 무한 이터러블이므로 done 프로퍼티를 생략한다.
          return { value: cur };
        }
      };
    }());
    
    // infiniteFibonacci는 무한 이터러블이다.
    for (const num of infiniteFibonacci) {
      if (num > 10000) break;
      console.log(num); // 1 2 3 5 8...2584 4181 6765
    }
    ```
    
- 제너레이터 함수 사용 시
    
    ```jsx
    // 무한 이터러블을 생성하는 제너레이터 함수
    const infiniteFibonacci = (function* () {
      let [pre, cur] = [0, 1];
    
      while (true) {
        [pre, cur] = [cur, pre + cur];
        yield cur;
      }
    }());
    
    // infiniteFibonacci는 무한 이터러블이다.
    for (const num of infiniteFibonacci) {
      if (num > 10000) break;
      console.log(num); // 1 2 3 5 8...2584 4181 6765
    }
    ```
    

### 46.5.2 비동기 처리

제너레이터 함수의 특성을 활용하면, 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있다.

즉, `then`/`catch`/`finally` 없이 비동기 처리 결과를 반환하도록 구현할 수 있다.

다음 예제는 완전하지는 않지만 이해를 위한 예시이다.

```jsx
// node-fetch는 node.js 환경에서 window.fetch 함수를 사용하기 위한 패키지다.
// 브라우저 환경에서 이 예제를 실행한다면 아래 코드는 필요 없다.
// https://github.com/node-fetch/node-fetch
const fetch = require('node-fetch');

// 제너레이터 실행기
const async = generatorFunc => {
  const generator = generatorFunc(); // ②

  const onResolved = arg => {
    const result = generator.next(arg); // ⑤

    return result.done
      ? result.value // ⑨
      : result.value.then(res => onResolved(res)); // ⑦
  };

  return onResolved; // ③
};

(async(function* fetchTodo() { // ①
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = yield fetch(url); // ⑥
  const todo = yield response.json(); // ⑧
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})()); // ④
```

위 예시는 다음과 같이 행동한다.

1. `async` 함수가 호출되면, 인수로 전달받은 제너레이터 함수 `fetchTodo`를 호출하여 제너레이터 객체를 생성하고, 상위 스코프의 `generator` 변수를 기억하는 클로저인 `onResolved` 함수를 반환한다. 이 함수는 즉시 호출되어 제너레이터 객체의 `next` 메서드를 처음 호출한다.
2. 첫 번째 `yield`까지 실행되고, `{done: false, value: Response 객체}` 를 반환받아  `fetch` 함수가 반환한 프로미스가 `resolve`한 `Response` 객체를 `onResolved` 함수의 인수로 전달하면서 재귀 호출한다.
3. `Response` 객체를 인수로 하여 `next` 메서드를 두 번째로 호출한다.
4. 두 번째 `yield`까지 실행되고, `{done: false, value: todo 객체}` 를 반환받아  `response.json` 메서드가 반환한 프로미스가 `resolve`한 `todo` 객체를 `onResolved` 함수의 인수로 전달하면서 재귀호출한다.
5. `todo` 객체를 인수로 하여 `next` 메서드를 세 번째로 호출한다.
6. 끝까지 실행되고, `{done: true, value: undefined}` 를 반환받아 `undefined`를 반환하며 종료한다.

## 46.6 async/await

ES8(ESMAScript 2017)에서 도입된 `async`/`await`는 프로미스의 `then`/`catch`/`finally` 후속 처리 메서드에 콜백 함수를 전달해서 마치 동기 처리처럼 프로미스를 사용할 수 있다.

위 예제는 `async`/`await`을 이용하여 다음과 같이 구현될 수 있다.

```jsx
const fetch = require('node-fetch');

async function fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

### 46.6.1 async 함수

`await` 키워드는 반드시 `async` 함수 내부에서 사용해야 한다. `async` 함수는 언제나 프로미스를 반환한다.

그렇기 때문에, 인스턴스를 반환해야 하는 클래스의 `constructor`는 `async` 메서드가 될 수 없다.

### 46.6.2 await 키워드

`await` 키워드는 프로미스가 `settled` 상태가 되기까지 대기하다가(실행을 일시 중지한다), `settled` 상태가 되면 `resolve`한 처리 결과를 반환한다.

하지만, 순차적으로 처리된다는 특성 때문에 서로 연관이 없이 개별적으로 수행되는 비동기 처리는 `await`보다는 `Promise.all` 등을 사용하여 처리하는 것이 좋다.

### 46.6.3 에러 처리

에러는 호출자 방향으로 전파된다. 즉, 콜 스택의 아래 방향으로(실행중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트)로 전파된다. 하지만 비동기 함수의 콜백 함수 호출은 비동기 함수가 하지 않았기 때문에, `try` `catch` 문을 이용하여 에러를 캐치할 수 없다.

`async`/`await` 사용 시, 프로미스를 반환하는 비동기 함수를 명시적으로 호출할 수 있기 때문에 호출자가 명확하고, `try` `catch`문을 사용할 수 있다.

`async` 함수 내에서 `catch`문을 사용해서 에러 처리를 하지 않으면, `async` 함수는 에러를 `reject`하는 프로미스를 반환한다. 이는 `Promise.prototype.catch` 후속 처리 메서드를 사용하여 캐치가 가능하다.

```jsx
const fetch = require('node-fetch');

const foo = async () => {
  const wrongUrl = 'https://wrong.url';

  const response = await fetch(wrongUrl);
  const data = await response.json();
  return data;
};

foo()
  .then(console.log)
  .catch(console.error); // TypeError: Failed to fetch
```