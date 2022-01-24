---
layout: post
title: 자바스크립트 프로토타입과 클래스
subtitle: 프로토타입을 만들어보고 클래스까지 알아보자
categories: javascript
tags: [javascript, typescript, TIL, prototype, class]
---

## 프로토타입과 프로토타입 상속

상속을 하는 이유??

내가 `user`라는 객체가 있고, `user`와 유사하지만 비슷한 `admin` 과 `guest`를 만들어야 했을 때, 유사한 객체를 만들 수 있을까??

> 프로토타입 상속을 이용하여 만들 수 있다

프로토타입의 예시 -> `__proto__` 로 만들 수 있다

```javascript
let animal = {
  eats: true,
};
let rabbit = {
  jumps: true,
};

rabbit.__proto__ = animal;
```

> `__proto__`는 Prototype용 getter/setter다

> 프로토타입은 읽기 전용이다

### this 는 무엇을 나타낼까?

먼저 코드를 보자

```javascript
// animal엔 다양한 메서드가 있습니다.
let animal = {
  walk() {
    if (!this.isSleeping) {
      alert(`Animal Walking.`);
    }
  },
  sleep() {
    this.isSleeping = true;
  },
};

let rabbit = {
  name: "White Rabbit",
  __proto__: animal,
};

// rabbit에 새로운 프로퍼티 isSleeping을 추가하고 그 값을 true로 변경합니다.
rabbit.sleep();

alert(rabbit.isSleeping); // true
alert(animal.isSleeping); // undefined (프로토타입에는 isSleeping이라는 프로퍼티가 없습니다.
```

그럼 여기서 `this`는 무엇을 나타낼까??

> method위치에 상관없이 `this` 는 항상 `.`앞에 있다

상속받은 메서드의 this는 animal이 아닌 실제 메서드가 호출되는 시점의 점(.) 앞에 있는 객체가 된다. 따라서 this에 데이터를 쓰면 animal이 아닌 해당 객체의 상태가 변화한다.

## 클래스란

> 클래스는 객체 지향 프로그래밍에서 특정 객체를 생성하기 위해 변수와 메소드를 정의하는 일종의 틀로, 객체를 정의하기 위한 상태(멤버 변수)와 메서드(함수)로 구성된다.

### 클래스의 기본 문법

new 연산자와 생성자 함수에서 new function 을 사용해보자

```javascript
class MyClass {
	constructor() {...}
	method1() {...}
	method2() {...}
	method3() {...}
	...
}
```

여기서 `constructor()`는 `new`에 자동으로 호출되어, 초기화 할 수 있다

class User{...} 가 어떤식으로 동작하는가 알아보면

1. `User`이라는 이름을 가지는 함수를 만들어, `constructor`에서 가져온다
2. `sayHi` 같은 클래스 내에서 정의한 method를 `User.prototype`에 저장한다

라는 과정을 거치게 된다.

이러한 과정을 코드로 살펴보면

```javascript
class User {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    alert(this.name);
  }
}

// 클래스는 함수입니다.
alert(typeof User); // function

// 생성자 method와 동일
alert(User === User.prototype.constructor); // true

// User.prototype에 저장
alert(User.prototype.sayHi); // alert(this.name);

// 현재 프로토타입에는 메서드가 두
alert(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi
```

이렇게 볼 수 있다.

### 클래스의 상속

`extends` 키워드를 통해 클래스의 상속을 할 수 있다.

먼저 `Animal` 이라는 클래스를 만들고, `Rabbit`을 `Animal`에 상속시켜 확장해보자

```javascript
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} 은/는 속도 ${this.speed}로 달립니다.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} 이/가 멈췄습니다.`);
  }
}

let animal = new Animal("동물");

class Rabbit extends Animal {
  hide() {
    alert(`${this.name} 이/가 숨었습니다!`);
  }
}

let rabbit = new Rabbit("흰 토끼");

rabbit.run(5); // 흰 토끼 은/는 속도 5로 달립니다.
rabbit.hide(); // 흰 토끼 이/가 숨었습니다!
```

이정도는 예전 객체지향에 대해서 배울 때 사용했던 부분이라 쉽게 이해가 된다

근데 여기서 `Rabbit` 클래스에 `stop` 이란 method를 자체적으로 정의하면 상속받은 method가 아닌 자신의 method가 호출하게 된다.

하지만 가끔, 부모 method의 일부만 변경하고 싶거나, 기능을 확장 하고 싶을 때가 있는데, 그 때 `super` 키워드를 사용하여 해결 할 수 있다.

```javascript
class Rabbit extends Animal {
  hide() {
    alert(`${this.name}가 숨었습니다!`);
  }

  stop() {
    super.stop(); // 부모 클래스의 stop을 호출해 멈추고,
    this.hide(); // 숨습니다.
  }
}
```

이런식으로, Rabbit.stop() 을 하게되면 부모클래스의 `stop`을 이용해 호출을 해서 멈춘 후 숨게 된다.

### 정적 Method 와 정적 Property

`prototype`이 아닌 클래스 함수 자체에서도 method를 설정 할 수 있다 -> static method

예시

```javascript
class User {
  static staticMethod() {
    alert(this === User);
  }
}

User.staticMethod(); // true
```

이런식으로 선언된 정적 method는 프로퍼티 형태로 직접 할당하는것과 동일한 역할을 한다

`User.staticMethod()` 호출 시 `this` 는 클래스 생성자인 `User`가 된다.

### Private, Protected Property & Method

객체지향 프로그래밍에서 Property & Method 는 두 그룹으로 분류된다

1. 내부 인터페이스 [Internal Interface] - 동일한 클래스 내의 다른 Method에 접근 할 수 있고, 클래스 밖에서는 접근할 수 없는 Property & Method -> Private
2. 외부 인터페이스 [External Interface] - 클래스 밖에서도 접근 가능한 Property & Method -> Public

### Protecting Property

Protected Property 이름의 앞에는 관행적으로 밑줄 [_] 이 붙는다.

예를들어, 커피머신이란 클래스를 만들어서 물의 양을 protect 해보자

```javascript
class CoffeeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power) {
    this._power = power;
  }
}

// 커피 머신 생성
let coffeeMachine = new CoffeeMachine(100);

// 물 추가
coffeeMachine.waterAmount = -10; // Error: 물의 양은 음수가 될 수 없습니다.
```

이런식으로 물의 양을 protect 하여 관행적으로 접근을 하지 말라고 소개할 수 있다

하지만 Java처럼 아예 접근이 불가능하게 private 하게 사용을 하려면 `_` 대신 `#` 을 사용하면 java에서 private 하게 사용했던 것 처럼 사용이 가능하다.

### Mix in

자바스크립트는 보통 단일상속만 허용한다. 하지만 여러 상속들을 한번에 밭을 수 있는방법이 존재한다

> 믹스인을 활용하여 다중상속을 받아보자

```javascript
let sayHiMixin = {
  sayHi() {
    alert(`Hello ${this.name}`);
  },
  sayBye() {
    alert(`Bye ${this.name}`);
  },
};

// 사용법:
class User {
  constructor(name) {
    this.name = name;
  }
}

// 메서드 복사
Object.assign(User.prototype, sayHiMixin);

// 이제 User가 인사를 할 수 있습니다.
new User("Dude").sayHi(); // Hello Dude!
```

이런식으로 상속없이 메서드를 복사하여, 믹스인을 활용하면 `User` 가 예시처럼 동시에 상속받아, 추가로 구현된 메서드 또한 사용 할 수 있는 모습을 볼 수 있다.
