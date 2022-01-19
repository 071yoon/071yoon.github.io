---
layout: post
title: 자바스크립트 함수의 심화학습
subtitle: 자바스크립트의 함수의 유형들에 대하여 알아보자
categories: javascript
tags: [javascript, typescript, TIL, functions]
---

## 나머지 매개변수와 스프레드 문법

### 나머지 매개변수
예전에 printf를 구현을 할 때, 가변인수라고 해서 `va args`를 써서 구현을 했던 적이 있는데, 그거에 비해서 정말 아니 이렇게 써도 된다고??? 할정도로 자바스크립트에서는 구현이 가능하다

```javascript
function sumAll(...args){
	let sum = 0;
	for(let arg of args)
		sum += arg;
	
	return (sum);
}
```

이런식으로도 구현이 가능하다!!

```javascript
function showName(firstName, lastName, ...titles) {
	//firstName = Bora;
	//LastName = Lee;
	// titles = ["Software Engineer", "Researcher"]
}

showName("Bora", "Lee", "Software Engineer", "Researcher");
```

### 스프레드 문법

배열안에 있는 요소들을 함수안에 사용을 하여 매개변수로 사용할 떄, 그냥 `max(arr)` 이런식으로 하면 안된다!! 
>뭔가 파이썬이면 될거같은데...

그래서 아까처럼 가변인자처럼 넘겨쓰는 스프레드 문법을 사용해야 된다

```javascript
let arr1 = [1, 3, 5, 2, 4];
let arr2 = [-3, 9, 4, 0];

alert(Math.max(...arr1, ...arr2, 3, 7)); //9
```

이런식으로 사용하면 된다!!

## New Function 문법

`new Function`을 통해 함수를 만들어 보자!

> `let func = new Function ([arg1, arg2, ...argN], functionBody);`

새로 만들어지는 함수는 들어오는 인수 arg1...argN 과 functionBody로 이루어지며 최대 장점은 런타임 시간에 동적으로 문자열을 사용해 함수를 만들 수 있는것!!

## 함수 바인딩

>setTimeout 을 진행하면 Method callback 진행 시 `this`정보가 사라지는데, 방지를 어떻게 할것인가!!

`this`가 사라지는 과정
```javascript
let user = {
	firstName: "Yoon",
	sayHi() {
		alert (`Hello ${this.firstName}!`);
	}
};

setTimeout(user.sayHi(), 1000);
```

이러면 당연히 `Hello Yoon!` 이 나와야될것 처럼 보이지만, `setTimeout`이 객체에서 분리가 되어 `user.sayHi`로 전달되어 `Hello undefined!` 가 나오게 된다.

### 해결방안1. 래퍼

>쇼미더머니 아님

```javascript
let user = {
	firstName: "Yoon",
	sayHi(){
		alert(`Hello ${this.firstName}!`);
	}
};
setTimeout(function(){
	user.sayHi();
}, 1000);
```

이러면 외부 렉시컬 환경에서 `user`를 받아오기 때문에 오류가 나지 않는다!!


### 해결방안2. 바인딩

```javascript
let user = {
  firstName: "Yoon"
};

function func() {
  alert(`Hello ${this.firstName}!`);
}

let funcUser = func.bind(user);

setTimeout(funcUser, 1000);
```

이런식으로 method를 user에 바인딩을 시켜버려서 묶어 버리는 방법이다. 

아무리봐도 바인딩이 래퍼보다 사용하기가 쉽고 활용도가 높아보인다!!

## 화살표 함수

화살표 함수에는 대체로 `this`가 없다. 화살표 함수에서 `this`를 사용하면 외부에서 값을 가져온다고 한다..!!

코드를 살펴보자

```javascript
let group = {
	title: "group1"
	students: ["Yoon", "Kim", "Lee"],

	showList(){
		this.students.forEach(
			student => alert(this.title + ': ' + student)
		);
	}
};
group.showList();
```

여기서 forEach에서 가리키느 화살표는 `this.title`의 화살표 밖에 있는 method인 showList가 가리키는 대상과 동일하다. 즉 `this.title == group.title` !!

> 화살표 함수는 new 와 함께 사용될 수 없음에 주의!
> 