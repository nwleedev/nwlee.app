---
title: '자바스크립트 프로토타입 체인'
description: '자바스크립트 프로토타입 개념에 대해서 정리했습니다.'
date: 2022-03-14T19:22:51+09:00
image: 'https://static.nwlee.app/public/L0PXVK00/6e31b167-bd80-4875-830b-9647a8f77853.png'
math:
license:
hidden: false
comments: true
draft: true
tags:
  ['Prototype', 'JavaScript', '자바스크립트', '프로토타입', '객체', '생성자']
categories: ['자바스크립트']
---

# 자바스크립트 프로토타입

## 생성자 함수

자바스크립트에서 객체를 만들기 위해서 중괄호로 이루어진 객체 리터럴 또는 생성자 함수를 사용할 수 있다. 속성 및 메소드가 같은 객체를 여러 개 생성하고 싶을 때 생성자 함수를 사용할 수 있다. 생성자 함수로 객체를 생성하려면 new 연산자가 필요하다.

```JavaScript
function Person(name, age, addr) {
  this.name = name;
  this.age = age;
  this.addr = addr;
  this.sayHi = function() {
    console.log("Hi!")
  }
}

const kim = new Person("Kim", 30, "Korea")
kim.sayHi()
// 콘솔에 Hi!가 출력된다.
```

생성자 함수로 객체가 만들어지는 과정

1. 비어있는 객체가 함수 내 this에 암시적으로 할당된다.
2. 함수 본문이 실행되면서 this에 새로운 속성 및 메소드가 추가된다.
3. this가 암시적으로 반환된다.

생성자 함수를 통해 재사용할 수 있는 객체 생성 코드를 작성할 수 있다. 익명 함수로는 생성자 함수를 선언할 수 없기 때문에 객체를 단 하나만 만들어야 할 때 사용해야 한다.

## 프로토타입 체인

자바스크립트는 프로토타입 기반 언어이다. 모든 객체들은 메소드와 속성을 상속받을 수 있는 프로토타입 객체를 가지고 있다. 상속되는 속성과 메소드는 객체의 생성자(생성자 함수)의 prototype이라는 속성에 정의되어 있다. 또한 프로토타입 객체는 각자의 상위 프로토타입 객체로부터 메소드와 속성을 상속받을 수 있다. 해당 객체에 메소드 또는 속성이 정의돼 있지 않으면 상위 객체의 프로토타입 객체로 계속 찾아가는 형태를 프로토타입 체인이라고 부른다.

자바스크립트 콘솔에서 생성자 함수에서 선언 및 초기화된 객체에 점(.)을 찍으면 여러가지 속성이 뜨는 것을 볼 수 있다.

<img src="https://static.nwlee.app/public/L0PXVK00/2259c4e4-57f5-403c-b5f2-76b51423f5d6.png" width="400" />

Person 생성자 함수 내 속성과 메소드 외에도 constructor, hasOwnProperty, valueOf 등의 속성이 표시된다. 이 속성들도 전부 정상적으로 작동한다.

```JavaScript
kim.hasOwnProperty("name")
// true가 출력된다.
```

constructor, hasOwnProperty, valueOf 등등의 메소드는 Object에 정의된 메소드이다.

<img src="https://static.nwlee.app/public/L0PXVK00/388df029-768a-4335-a9b7-d1a6f7d20efe.png" width="400" alt="Methods in Object">

Object에 정의되어 있는 메소드를 `kim`과 같은 객체에서 호출하는 과정은 다음과 같다.

1. kim 객체에 해당 메소드가 있는지 확인한다.
2. kim 객체에 메소드가 없으면 kim의 프로토타입 객체, Person에 메소드가 있는지 확인한다. Person에도 메소드가 없으면 프로토타입 객체의 상위 프로토타입 객체에 메소드가 있는지 재차 확인한다.
3. Object의 프로토타입 객체에 해당 메소드가 있으면 메소드가 실행된다.

프로토타입 체인에서는 한 객체가 다른 상위 객체를 상속받을 때 객체의 메소드와 속성들이 복사되지 않는다. 각자 상위 프로토타입 객체에 해당 메소드나 속성이 있는지 확인하는 과정을 거친다.

속성과 메소드가 상속받을 수 있도록 하기 위해서는 속성 및 메소드를 prototype 객체에 정의하는 것이다. 예를 들어 Object의 hasOwnProperty는 Object.prototype 객체에 정의되어 있지만 keys, values 메소드는 Object 내에 정의되어 있다. Object의 프로토타입 객체를 상속받는 생성자 함수로 선언된 객체는 keys, values 메소드를 사용할 수 없다.

Array, String, Date, Number와 같은 전역 객체도 Object의 프로토타입 객체에 정의된 메소드와 속성을 상속받는다.

<img src="https://static.nwlee.app/public/L0PXVK00/9ce51734-905b-4c4b-8f65-012fb8b8c2c5.png" width="400" alt="내장 객체">

<img src="https://static.nwlee.app/public/L0PXVK00/4c0697da-e83a-481c-a332-0284dec8dd5f.png" width="400" alt="내장 객체 사례"/>

생성자 함수를 예측할 수 없는 경우 객체에서 constructor 메소드로 동일한 형태의 새로운 객체를 생성할 수 있다. constructor 속성은 원본 생성자 함수를 참조한다. constructor 메소드는 각 생성자 함수의 prototype 객체에 정의되어 있어서, 생성된 객체에서 해당 메소드를 호출할 수 있다.

```JavaScript
const john = kim.constructor("John", 40, "Japan")
john.sayHi()
// Hi!가 출력된다.
```

## 프로토타입 수정

프로토타입 객체에 메소드를 추가하여, 해당 생성자에서 생성된 객체에서 사용하도록 할 수 있다. 프로토타입 객체는 모든 객체에서 공유하기 때문에 메소드를 새로 정의하는 순간 접근할 수 있다.

```JavaScript
Person.prototype.sayBye = function() {
  console.log("Bye!")
}
kim.sayBye()
// Bye!가 출력된다.
```

## 프로토타입 상속

직접 정의한 생성자 함수로 만든 객체를 상속하려면 상속받는 객체의 생성자 함수에서 상속할 객체의 call 메소드를 사용해야 한다.

```JavaScript
function Shape(width, height) {
  this.width = width;
  this.height = height;
}

function Rect(width, height) {
  Shape.call(this, width, height)
}
Rect.prototype.size = function() {
  return this.width * this.height
}

const rect = new Rect(10, 20)
console.log(rect.size())
// 200(10 * 20)이 출력된다.
```

상속받는 생성자 함수를 선언할 때는 상속할 생성자 함수의 call 메소드를 호출해야 한다. 메소드 첫번째 매개변수에는 this가 전달된다. 이는 다른 곳에서 정의된 함수를 현재 환경(컨텍스트)에서 실행할 수 있도록 한다. 상속받는 생성자 함수 또한 같은 매개변수가 필요하다.

그 다음 상속할 생성자의 프로토타입 메소드를 상속받게 하고, 상속받은 생성자 프로토타입 객체의 constructor도 자신을 참조하도록 해야 한다.

```JavaScript
Rect.prototype = Object.create(Shape.prototype)
Rect.prototype.constructor = Rect
```

## 객체 리터럴

중괄호나 `new Object()`로 객체를 선언한 경우 해당 객체의 프로토타입은 Object의 프로토타입을 상속받는다. 선언된 객체에서 toString 메소드를 실행하면 `[object Object]`가 출력되는데 이는 프로토타입 체인을 통해 `Object.toString` 메소드가 실행되었기 때문이다.

Object.prototype 객체에는 Object에서 상속할 메소드가 정의되어 있고, 객체는 프로토타입 객체를 상속받는다.

<img src="https://static.nwlee.app/public/L0PXVK00/bbb185aa-823a-44d5-9021-f735c2a8e117.png" width="400" alt="객체 리터럴" />

## 네이티브 프로토타입

기존에 정의된 객체의 프로토타입을 수정할 수 있다. 프로토타입을 수정하면 프로토타입 객체를 상속받는 모든 객체가 영향을 받는다. 주로 폴리필을 만들 때 주로 사용한다.

```JavaScript
const array = [1,2,3]
console.log(array.map(elem => elem ** 2))
// 1,4,9가 출력된다.

Array.prototype.map = function(fn) {
  return "Hello World!"
}
console.log(array.map(elem => elem ** 2))
// map 메소드의 콜백 함수와 상관없이 Hello World!가 출력된다.
```

```
폴리필은 자바스크립트 명세서에 있는 메소드와 동일한 기능을 하는 메소드 구현체를 의미한다. 특정 자바스크립트 엔진에서 기능이 구현되지 않았을 때 폴리필이 사용된다.
```
