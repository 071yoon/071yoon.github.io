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
