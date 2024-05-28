# 46장 제너레이터와 async/await

## 46.1 제너레이터란?

ES6에서 도입된 제너레이터(generator) 함수는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수입니다.

### 제너레이터 함수와 일반 함수의 차이

- **일반 함수**:
  - 호출하면 제어권이 함수에게 넘어가고 함수 코드를 일괄 실행합니다.
  - 호출자는 함수를 호출한 이후 함수 실행을 제어할 수 없습니다.

- **제너레이터**:
  - 제너레이터 함수는 함수 실행을 함수 호출자가 제어할 수 있습니다.
  - 함수의 제어권을 함수가 독점하는 것이 아니라 함수 호출자에게 양도(yield)할 수 있습니다.
  - 함수 호출자와 양방향으로 함수의 상태를 주고받을 수 있습니다.

### 제너레이터 함수의 정의

제너레이터 함수는 `function*` 키워드로 선언하며, 함수 내부에는 하나 이상의 `yield` 표현식을 포함해야 합니다.

```javascript
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}
```


## 46.2 제너레이터 객체

- 제너레이터 함수를 호출하면 함수 코드 블록을 실행하는 것이 아니라 이터러블이면서 이터레이터인 제너레이터 객체를 생성해 반환합니다.

```javascript
const generator = genFunc();
console.log('next' in generator); // true
console.log(generator.next()); // { value: 1, done: false }
```

- 제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 `return`, `throw` 메서드를 추가로 갖습니다.

- next 메서드를 호출하면 제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고 yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 객체를 반환합니다.

- return 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 객체를 반환합니다.

## 46.3 제너레이터의 일시 중지와 재개

- `yield` 키워드는 제너레이터 함수의 실행을 일시 중지시키거나 yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환합니다.

## 46.4 제너레이터의 활용

### 46.4.1 이터러블의 구현

- 제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블을 구현할 수 있습니다.

### 46.4.2 비동기 처리

- 제너레이터 함수와 프로미스를 사용한 비동기 처리를 동기처럼 구현할 수 있습니다.

## 46.5 async/await

### 46.5.1 async 함수

- `await` 키워드는 반드시 async 함수 내부에서 사용해야 하며, async 함수는 언제나 프로미스를 반환합니다.

```javascript
async function fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';
  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
}
```

### 46.5.2 await 키워드

- `await` 키워드는 프로미스가 settled 상태가 될 때까지 대기하며, 프로미스가 resolve한 결과를 반환합니다.

### 46.5.3 에러 처리

- async/await에서 에러 처리는 `try...catch문`을 사용할 수 있으며, 에러 처리가 보다 명확하고 간단해집니다.

```javascript
async function foo() {
  try {
    const response = await fetch('https://wrong.url');
    const data = await response.json();
  } catch (error) {
    console.error(error);
  }
}
```

### 추가

