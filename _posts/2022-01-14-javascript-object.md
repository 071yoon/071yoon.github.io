---
layout: post
title: 자바스크립트 객체 - 1
subtitle: 자바스크립트 객체란 무엇인가
categories: javascript
tags: [javascript, typescript, TIL]
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

## 참조에 의한 객체 복사


### 