# 26. ES6 함수의 추가 기능

## 26.1 함수의 구분

이전과 무엇이 달라졌는가? 이전에는 일반적인 함수로서 호출도 가능, new 연산자와 함께 호출하여 인스턴스를 생성할 수 있는 생성자 함수로서 호출도 가능, 객체에 바인딩 되어 메서드로서 호출도 가능

**ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다(callable이면서 constructor)**

```jsx
var foo = function() {
	return 1;
}
foo();//1
new foo();// foo {}
var obj = { foo: foo };
obj.foo();//1
```

⇒ but! 성능면에서 손해

```jsx
//프로퍼티 f에 바인딩된 함수는 callable이며 constructor
var obj={
    x:10,
    f:function(){return this.x;}
}
//프로퍼티 f에 바인딩된 함수를 메서드로 호출
console.log(obj.f())//10
//프로퍼티 f에 바인딩된 함수를 일반 함수로서 호출
var bar=obj.f
console.log(bar())//undefined
console.log(new obj.f())// f {}
```

객체에 바인딩된 함수가 constructor라는 것은 객체에 바인딩된 함수가 prototype프로퍼티를 가지며, 프로토타입 객체도 생성한다는 것을 의미함

함수에 전달되어 보조 함수의 역할을 수행하는 콜백함수도 마찬가지(콜백함수도 constructor이기 때문에 불필요한 프로토타입 객체를 생성) 

그래서 어떤 문제가 생기는데??

**1. 불필요한 메모리 사용**

프로토타입 객체는 모든 함수에 대해 생성되기 때문에, 생성된 함수의 수가 많아질수록 불필요한 프로토타입 객체도 많아집니다. 이로 인해 메모리가 낭비될 수 있습니다. 콜백 함수와 같은 짧은 수명의 함수들이 자주 생성되고 폐기되는 경우, 이러한 메모리 낭비는 더욱 두드러집니다.

**2. 성능 저하**

모든 함수가 프로토타입 객체를 가지므로, 자바스크립트 엔진은 이러한 객체들을 관리하고 추적하는데 추가적인 자원을 소모하게 됩니다. 특히, 콜백 함수나 간단한 유틸리티 함수들이 자주 호출되고 생성될 때마다 이와 같은 프로토타입 객체가 생성되는 것은 불필요한 오버헤드로 작용할 수 있습니다.

**3. 엔진 최적화의 어려움**

자바스크립트 엔진은 일반적으로 객체와 함수의 생성과 관리, 가비지 컬렉션 등을 최적화하려고 노력합니다. 하지만 모든 함수가 생성자 함수로 간주되고 프로토타입 객체를 가지는 경우, 엔진이 불필요한 프로토타입 객체를 최적화하기 어려워집니다. 이는 전반적인 성능 저하를 유발할 수 있습니다.

이러한 문제를 해결하기 위해 ES6에서는 함수를 사용 목적에 따라 세 가지 종류로 구분!!!

| ES6함수의 구분 | constructor | prototype | super | arguments |
| --- | --- | --- | --- | --- |
| 일반 함수(Normal) | O | O | X | O |
| 메서드(Method) | X | X | O | O |
| 화살표 함수(Arrow) | X | X | X | X |

**메서드와 화살표 함수는 non-constructor!!** 

## 26.2 메서드

메서드 축약 표현으로 정의된 함수만을 의미

```jsx
const obj={
    x:1,
    foo(){return this.x;},
    bar:function(){
		  return this.x;
	  }
}
console.log(obj.foo());//1
console.log(obj.bar())//1
new obj.foo()
// Uncaught TypeError: obj.foo is not a constructor   
new obj.bar()
// bar {}
```

ES6 메서드는 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고, 프로토타입도 생성하지 않음

(참고로 표준 빌트인 객체가 제공하는 프로토타입 메서드와 정적 메서드는 모두 non-constructor이다)

**ES6메서드는 자신을 바인당한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다**

```jsx
const base ={
    name:"Lee",
    sayHi(){
        return `Hi! ${this.name}`;
    }
}
const derived={
    __proto__:base,
    //sayHi는 ES6메서드. [[HomeObject]]를 갖는다
    //sayHi의 [[HomeObject]]는 sayHi가 바인딩된 객체인 derived를 가리키고
    //super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킨다
    sayHi(){
        return `${super.sayHi()}. how are you doing?`;
    }
}
console.log(derived.sayHi())
//Hi! Lee. how are you doing?
```

ES6 메서드가 아닌 함수는 super 키워드를 사용할 수 없다(내부 슬롯 [[HomeObject]]를 갖지 않기 때문)

```jsx
const derived={
    __proto__:base,
    sayHi:function(){
        return `${super.sayHi()}. how are you doing?`;
    }
}
//Uncaught SyntaxError: 'super' keyword unexpected here
```

메서드는 super를 추가하고, constructor제거

**메서드를 정의할 때 프로퍼티 값으로 익명 함수 표현식을 할당하는 ES6 이전의 방식은 사용하지 않는 것이 좋다.**

## 26.3 화살표 함수

콜백 함수 내부에서 this가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용

### 26.3.1 화살표 함수의 정의

**함수 정의**

화살표 함수는 함수 선언문으로는 정의할 수 없고 함수 표현식으로 정의해야한다. 

**매개변수 선언**

매개변수가 여러개인 경우 소괄호()안에 매개변수를 선언

```jsx
const arrow = (x, y) => { ... }
//매개변수가 한 개인 경우 소괄호 생략가능
const arrow = x => { ... }
//매개변수가 없는 경우 소괄호 생략 불가
const arrow = () => { ... }
```

**함수 몸체 정의**

```jsx
const power= x => x ** 2;
power(2) //4
const power = x => {return x **2 };
//함수 몸체를 감싸는 중괄호{}를 생략한 경우 함수 몸체 내부의 문이 표현식이 아닌 문이라면 에러가 발생(표현식이 아닌 문은 반환불가)
const arrow = () => const x = 1; //SyntaxError
// 맞게 고치면?
const arrow =() => { return const x = 1; };
```

객체 리터럴을 반환하는 경우 객체 리터럴을 소괄호로 감싸 주어야 함

```jsx
const create = ( id, content ) => ({id,content});
create(1,'JavaScript');//{id: 1, content: 'JavaScript'}
// ===
cosnt create = (id, content) => {return {id, content}; };
//객체 리터럴을 소괄호()로 감싸지 않으면 객체 리터럴의 중괄호{}를 함수 몸체를 감싸는 중괄호{}로 잘못 해석함
const create = (id, content) =>{id,content};
create(1,'JavaScript')//undefined
```

화살표 함수도 즉시 실행함수로 사용할 수 있음

```jsx
const person =(name=>({
sayHi(){return `Hi? My name is ${name}.`;}
}))('Lee');
console.log(person.sayHi());//Hi My name is Lee.
```

```jsx
//ES5
[1,2,3].map(function(v){
	return v*2;
	})

//ES6
[1,2,3].map(v=>v*2);
```

### 26.3.2 화살표 함수와 일반 함수의 차이

1. 화살표 함수는 인스턴스를 생성할 수 없는 `non-constructor`
2. 중복된 매개변수 이름을 선언할 수 없다.
    1. 일반 함수는 중복된 매개변수 이름을 선언해도 에러가 발생하지 않음(단, strict mode에서는 에러 발생)
3. 화살표 함수는 함수 자체의 this, arguments, super, new, target 바인딩을 갖지 않는다
    1. 화살표 함수 내부에서 this, arguments, super, new, target을 참조하면 스코프 체인을 통해 상위 스코프의 this, arguments, super, new, target을 참조
    2. 만약 화살표 함수와 화살표함수가 중첩되어 있다면 상위 화살표 함수에도 this, arguments, super, new, target바인딩이 없으므로 스코프 체인 상에서 가장 가까운 함수 중에서 함수가 아닌 this, arguments, super, new, target을 참조

### 26.3.3 this

this 바인딩은 함수의 호출방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.

함수를 정의할 때 this에 바인딩 할 객체가 정적으로 결정되는 것이 아니고, 함수를 호출할 때 함수가 어떻게 호출되었는지에 따라 this에 바인딩할 객체가 동적으로 결정된다. 

```jsx
class Prefixer{
    constructor(prefix){
        this.prefix=prefix;}
    add(arr){
        return arr.map(function(item){
            return this.prefix+item;});
    }}
    const prefixer=new Prefixer('-webkit-');
    console.log(prefixer.add(['transition','user-select']));
   //Uncaught TypeError: Cannot read properties of undefined (reading 'prefix')
```

화살표 함수를 사용하면 

```jsx
class Prefixer{
    constructor(prefix){
        this.prefix=prefix;}
    add(arr){
        return arr.map(item=>this.prefix+item);
    }}
    const prefixer=new Prefixer('-webkit-');
    console.log(prefixer.add(['transition','user-select']));
    //['-webkit-transition','-webkit-user-select']
```

화살표 함수는 함수 자체의 this 바인딩을 갖지 않음. 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조 ⇒ `lexical this`

### 26.3.4 super

화살표 함수는 함수 자체의 super 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 super를 참조하면 this와 마찬가지로 상위 스코프의 super를 참조한다.

```jsx
class Base{
    constructor(name){
        this.name=name;
    }
    sayHi(){
        return `Hi, ${this.name}`;
    }
}
class Derived extends Base{
    sayHi=()=>`${super.sayHi()} how are you doing?`;
}
const derived = new Derived('Lee')
console.log(derived.sayHi())
//Hi, Lee how are you doing?
```

super는 내부 슬롯 [[HomeObject]]를 갖는 ES6메서드 내에서만 사용 가능한 키워드

### 26.3.5 arguments

화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않는다.

화살표 함수 내부에서 arguments를 참조하면 this와 마찬가지로 상위 스코프의 arguments를 참조

**화살표 함수로 가변인지 함수를 구현할 때는 반드시 Rest 파라미터를 사용해야 함**

## 26.4 Rest 파라미터

### 26.4.1 기본 문법

rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달 받는다

```jsx
function foo(...rest){
console.log(rest)
}
foo(1,2,3,4,5)
```

일반 매개변수와 rest 파라미터는 함께 사용할 수 있다. 이때 함수에 전달된 인수들은 매개변수와 rest파라미터에 순차적으로 할당됨

Rest 파라미터는 반드시 마지막 파라미터여야 함(+단 하나만 선언 가능)

### 26.4.2 Rest 파라미터와 arguements 객체

arguments객체는 호출시 전달된 인수들의 담고 있는 순회 가능한 유사배열객체이며 함수 내부에서 지역변수처럼 사용할 수 있다.

ES6에서는 rest파라미터를 사용하여 가변 인자 함수의 인수 목록을 배열로 직접 전달 받을 수 있다.⇒유사 배열 객체인 arguments 객체를 배열로 변환하는 번거로움 피할 수 있음

함수와 ES6 메서드는 rest 파라미터와 arguments객체를 모두 사용할 수 있다. 

화살표 함수는 함수 자체의 arguments객체를 갖지 않는다.⇒ **화살표 함수로 가변인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용**

## 26.5 매개변수 기본 값

ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화 할 수 있다.

```jsx
function sum(x=0,y=0){
return x+y;
}
console.log(sum(1,2))//3
console.log(sum(1))//1
```

매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효

매개변수 기본값은 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티와 arguments객체에 아무런 영향을 주지 않음