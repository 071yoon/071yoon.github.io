---
layout: post
title: 자바스크립트 객체
subtitle: 자바스크립트 객체란 무엇인가
categories: javascript
tags: [javascript, typescript, method, TIL]
---

최근에 마음이 맞는 사람들과 프로젝트를 진행해 보기로 했다. `Decrypto` 라는 보드게임을 웹 환경으로 만들어 보면 어떠겠는가 해서 시작된 프로젝트였다. 생각보다 관심이 있는 사람들이 많아 인원을 다 모으고 회의를 진행을 해 보았는데... 아뿔싸... 제대로 웹 개발을 해본 사람이 없는것! 그래서 프로젝트 바로 시작하는것은 잠시 미뤄두고 스터디를 진행을 해 보기로 하였다. 인원도 많았기에, 팀별로 `React`, `Type Script`, `Node.js` 이런식으로 인원을 쪼개 2주간 진행을 한 후 통합하여 실제로 웹 프로젝트를 진행해 보기로 했다.

그렇게 우리팀인 `TypeScript` 스터디팀은 무작정 코드리뷰도 해보려고하고, 코드아카데미에서 따라도 해보았고 그렇게 결정 난 사안이 [아직 자바스크립트도 제대로 못한다] 였다. 그래서 이론적인 부분에서 자바스크립트를 조금 더 공부하고자 포스팅을 시작한다.

## Javscript - 객체란 무엇인가

- 객체란 다양한 데이터를 담을 수 있는 형태

- 키로 구분된 데이터의 집합이나 개체를 저장 할 수 있다


### Property

- 중괄호 `{...}` 를 통해 만들 수 있으며, 안에 `key[string]: value[all types]`에 해당한 프로퍼티가 들어가야 한다
	```javascript
	let user = new Object();
	let user = {
		name: "Yoon",
		age: 25,
		"test case": true
	};
	```
	>const로 선언된 상수 객체가 수정 될 수 있음에 주의

### 대괄호 표기법

- 만약 내가 `user`에 `test case` 를 불러오고 싶어도 `user.test case` 라 선언 시 오류가 나기 때문에, user["test case"] 이런식으로 대괄호로 읽어야 한다
- 혹은
	```javascript
	let tmp = "test case"
	user[tmp] = true
	```
	처럼 선언하여 사용 할 수 있다

### `in` 연산자로 프로퍼티 존재 확인

- 존재하지 않는 프로퍼티에 접근해도 에러가 발생하지 않고 `undefined`로 반환됨에 주의
  ```javascript
  let user = {name: "Yoon", age: 25};
  alert("age" in user); //TRUE
  alert("work" in user);//FALSE
  for (let key in user){
	  alert(key);//name, age
	  alert(user[key]);//Yoon, 25
  }
  ```
- 정렬방식은 정수 프로퍼티가 자동으로 정렬되고, 그 외는 추가 순서로 정렬됨에 주의
  ```javascript
  let codes = {
	  "49": "Germany",
	  "41": "Swiss",
	  "44": "England",
	  //..
	  "1": "Korea"
  };
  for(let code in codes){
	  alert(code);//1, 41, 44, 49
  }
  ```

## 참조

### 참조에 의한 객체복사

객체와 원시타입의 차이는 `참조에 의해` 저장되고 복사된다는 점이다

- 예시
	```javascript
	let message = "Hello!";
	let phrase = message;
	```
	이렇게 하면 message 와 phrase 둘 다 "Hello!"라는 문자열이 저장된다
- 이제 객체에 대한 참조를 해보면
	```javascript
	let user = { name: "YeongGi"};
	let admin = user;
	```
	이런식으로 참조하여 복사를 하게되면, 이후 user의 값을 임의로 변경을 하여도 admin또한 변경이 된다

### 참조에 의한 비교
객체 비교 시 동등연산자 `==` 와 `===`의 차이를 알아보자
- 원래는 비교 시 피연산자인 두 객체가 동일한 경우 참을 반환한다
	```javascript
	let a = {};
	let b = a;//참조에 의한 복사
	alert (a == b);
	alert (a === b);
	```
	둘 다 참을 반환한다
- 독립된 객체의 비교
	```javascript
	let a = {};
	let b = {};
	alert ( a == b);//false
	```

### 객체의 복사
기존의 객체와 같으면서 독립적인 객체를 만들고 싶다면 두가지정도 방법이 존재한다
1. `for loop` 을 돌며 복사
2. `Object assign`을 활용

```javascript
let user = {
	name: "Yoon",
	age: 25
};

let clone = {};//새로운 빈 객체

for (let key in user){//1번의 방법
	clone[key] = user[key];
}

Object.assign(user, clone);//2번의 방법
```

## 가비지 컬렉션

눈에 보이지 않는 메모리 관리를 한번에 해결해주는 방법으로, Java에서 사용했던것과 유사하다.
>`Reachable`한 값은 메모리에서 삭제하지 않음에 주의

### Point
- 가비지 컬렉션은 엔진이 자동으로 수행하므로 억지로 실행 할 수는 없다
- 객체는 도달 가능한 상태일 때 메모리에 남는다
- 참조된다고 도달이 되는것은 아니다
  - 서로 연결된 객체들도 도달 불가능 할 수 있다

## Method 와 this
객체는 개체를 표현하고자 할 때 생성되며, 객체의 프로퍼티로 함수를 할당해 객체에 할당 시킬 수 있다

## Method 생성

`Method` 생성에는 다양한 방법들이 있고 하나씩 나열해보자
```javascript
let user = {
	name: "Yoon",
	age: 25
};

user.sayHi = function() {//방법1
	alert("Hello!");
};

function sayHi() {//방법2
	alert("Hello!");
};
user.SayHi = sayHi;

user = {//방법3
	SayHi(){
		alert("Hello!");
	}
};
```
### `this`의 사용법
아까 사용했던 `user.sayHi()` 같은 경우에, 내부 코드에서 객체를 사용하려면 어떻게 사용해야 될까??

`this` 키워드를 사용하여 객체에 접근 할 수 있다

```javascript
let user = {
	name: "Yoon",
	age: 25,

	sayHi() {
		alert(this.name);
//or	alert(user.name);
	}
};
```
처럼 사용 할 수 있으며, 다른 프로그래밍 언어와 다르게 모든 함수에 `this`를 사용 할 수 있다

> this가 없는 화살표 함수
```javascript
let user = {
	firstName: "Yoon",
	sayHi(){
		let arrow = () => alert(this.firstName);
		arrow();
	}
};
user.sayHi(); //Yoon
```
별개의 this가 만들어 지는것을 원하지 않고, 외부 컨텍스트에서 this를 사요하고 싶을 때 화살표 함수를 사용한다.

## New 연산자와 생성자함수
객체 리터럴을 사용하여 객체를 쉽게 만들 수 있지만, 여러개 객체를 관리 할 때 `new` 연산자와 생성자를 통해 유사한 객체를 복수로 만들 수 있다

### 생성자 함수
특징
1. 함수 이름의 첫글자는 대문자로 시작
2. 반드시 `new` 연산자를 붙여 실행한다
예시
```javascript
function User(name){
	this.name = name;
	this.isAdmin = false;
}
let user = new User("Yoon");
alert(user.name); //Yoon
alert(user.isAdmin);//False
```

### 생성자 내 Method

생성자 함수를 사용하면 매개변수를 이용해 객체 내부를 구성 할 수 있다.
> 유연하게 사용 할 수 있게 도와준다

```javascript
function User(name) {
	this.name = name;

	this.sayHi = function() {
		alert("My name is " + this.name + ".");
	};
}
let yoon = new User("Yoon");

yoon.sayHi(); //My name is Yoon.
```

