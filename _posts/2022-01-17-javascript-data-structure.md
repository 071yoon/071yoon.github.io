---
layout: post
title: 자바스크립트 자료구조와 자료형
subtitle: 자바스크립트의 자료형에 대하여 알아보자
categories: javascript
tags: [javascript, typescript, TIL, data structure]
---

## 자료구조와 자료형

기본적으로, 프로그래밍 언어들에 대한 기본지식이 있는 상태로 공부를 진행하여, 기본적인 유형인 부분들은 스킵하도록 하였다

### 원시값과 객체의 차이

원시값이란
- 원시형의 값
- 종류로는 `string, number, bigint, boolean, symbol, null, undefined `으로 총 7가지가 올 수 있다

객체
- Property에 다양한 종류의 값을 저장 할 수 있다
- `{name: "Yoon", age: 25}`와 같이 {}를 사용해 만들 수 있다
- 자바스크립트에는 여러 종류의 객체가 있으며, 함수도 객체의 일종이다

### 문자열
> 자바스크립트는 글자 하나만 저장 할 수 있는 `char` 형의 자료형이 없다!

따옴표의 종류가 `'` 와 `"` 으로 두개 있고, 둘 다 사용 할 수 있으며, 백틱으로도 감쌀 수가 있다. 그리고 백틱에는 특별한 기능이 있는데, 표현식을 ${...} 으로 감싸고 이를 백틱에 감싼 문자열에 넣어주면, 표현식을 쉽게 삽입을 할 수 있게 해준다
> `template literal` 이라 부른다
예시를 보면
```javascript
function sum(a, b){
	return (a + b);
}
alert(`1 + 2 = ${sum(1, 2)}.`);
```
또한 백틱을 이용하여 문자열을 여러줄로 작성 할 수 도 있다
```javascript
let guests = `Guests:
	* Kim
	* Yoon
	* Lee
`;
```

### 배열

배열 선언에는 두가지 방법이 있다
```javascript
let arr = new Array();//no.1
let arr = [];//no.2
```
- 그리고 두번째 방법으로 대괄호 안에, 초기화를 시켜주는 것 또한 가능하다.
- 배열 요소의 자료형에는 제약이 없다
	```javascript
	let arr = ['apple', {name: 'yoon'}, true, function() {alert('hello');}];
	arr[3](); //hello
	```

#### 반복문
`for`문은 배열을 순회 할 때 사용하는 가장 쉬운 방법이다
배열에 적용하는 순회 방법중 가장 쉬운 for...of를 사용해 보자
```javascript
let fruits = ['apple', 'orange', 'pear'];
for(let fruit of fruits){
	alert (fruit);//apple -> orange -> pear
//	or alert(arr[key]);
}
```
`for..of`를 사용하면 현재 요소의 인덱스는 얻을 수 없고 값만 얻을 수 있다. 하지만 구현하기가 쉽고 문법도 짧아 배열의 요소를 대상으로 반복 작업을 할 때는 매우 편리하다

### `iterable` 객체

- 반복 가능한 객체는 배열을 일반화 한 객체이다
- `iterable`을 사용하여 어떤 객체에서든 `for..of` 반복문을 적용 시킬 수 있다

#### `Symbol.iterator`
1. `range` 를 iterable로 만들기 위해 객체에 `Symbol.iterator` 를 추가한다
2. `for..of` 가 시작되면 `Symbol.iterator`를 호출 해 조건성을 만족시킨다
3. `for..of`는 반환된 객체만을 대상으로 동작한다
4. `for..of`에 다음값이 필요하면 iterator의 `next()`를 호출한다
5. `next()`의 반환값은 `{done: Boolean, value: any}` 와 같은 형태이고 `done === true`면 반복이 종료되었다는 뜻이다

```javascript
let range = {
  from: 1,
  to: 5
};

// 1. for..of 최초 호출 시, Symbol.iterator가 호출됩니다.
range[Symbol.iterator] = function() {

  // Symbol.iterator는 이터레이터 객체를 반환합니다.
  // 2. 이후 for..of는 반환된 이터레이터 객체만을 대상으로 동작하는데, 이때 다음 값도 정해집니다.
  return {
    current: this.from,
    last: this.to,

    // 3. for..of 반복문에 의해 반복마다 next()가 호출됩니다.
    next() {
      // 4. next()는 값을 객체 {done:.., value :...}형태로 반환해야 합니다.
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// 이제 의도한 대로 동작합니다!
for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```

### Date 객체와 날짜
날짜를 저장 할 수 있고, 관련된 함수를 제공하는 `Date`에 대해 알아보자
- 객체의 생성
	- `new Date()` 를 호출해 새로운 객체를 만들어 사용 할 수 있다
- 날짜 찾기
  - getFullYear() -> 네자릿수 연도
  - getMonth() -> 0부터 11까지의 월
  - getDate() -> 1부터 31까지 날
  - getHours()
  - getMinutes()
  - getSeconds()
  - getMillioseconds()
  - getDay() -> 0부터 6까지, 일요일부터 토요일
  - getTime() -> 주어진 시간과 1970년 1월 1일 00초 사이의 간격
  - getTimezoneOffset() -> 현지 시간과 표준 시간의 차이를 반환

### JSON과 Method

복잡한 객체를 사용하고 있어, 네트워크를 통해 객체를 보내거나 로깅 목적으로 객체를 출력해야 할 때 사용하는 방법으로 `JSON`을 사용 할 수 있다

#### JSON.stringify
- JSON,stringify -> 객체를 JSON으로 바꿔준다
- JSON.parse -> JSON을 객체로 바꿔준다

```javascript
let student = {
	name: 'Yoon',
	age: 25,
	isAdmin: false,
	courses: ['html', 'css', 'js'],
	wifi:null
};

let json = JSON.stringify(student);

alert(typeof json); //string
alert(json);//student의 내용을 string으로 출력
```
