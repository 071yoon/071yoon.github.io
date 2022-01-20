---
layout: post
title: 자바스크립트 객체 프로퍼티 설정
subtitle: 객체의 프로퍼티에 대해서 알아보자
categories: javascript
tags: [javascript, typescript, TIL, property]
---

# 객체 프로퍼티

프로퍼티란 단순히 `key-value` 가 아닌 더 유연한 자료구조로 사용 될 수 있으며, `getter`와 `setter` 등에 대해서 학습해보자

## 프로퍼티 플래그와 생성자

객체 프로퍼티는 값과 플래그라는 속성을 가진다
- writable - true 라면 수정이 가능하다
- enumerable - true 라면 반복문으로 나열 할 수 있다
- configurable - true 라면 삭제나 플래그 수정이 가능하다

평범한 방식으로 설계를 한다면, 프로퍼티는 모두 `true`이며, 언제든 수정 될 수 있다

`let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);`

여기서 
- `obj` 란 정보를 얻고자 하는 객체
- `propertyName` 이란 정보를 얻고자 하는 객체 내 프로퍼티
를 말한다

예시

```javascript
let user = {
  name: "Yoon"
};

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

alert( JSON.stringify(descriptor, null, 2 ) );
/* property descriptor:
{
  "value": "Yoon",
  "writable": true,
  "enumerable": true,
  "configurable": true
}
*/
```

이 상태에서 `Object.defineProperty`로 플래그를 변경 할 수 있다

## getter 와 setter

객체의 프로퍼티를 나누는 두 분류로 볼 수 있다.
1. 데이터 프로퍼티 -> 현재까지 모두 사용한 프로퍼티
2. 접근자 프로퍼티 -> 새로운 종류로, 함수의 값을 획득하고 설정하는 역할을 담당한다

```javascript
let obj = {
  get propName() {
    // getter, obj.propName실행시
  },

  set propName(value) {
    // setter, obj.propName = value실행시
  }
};
```

그럼 이걸 어떤식으로 활용 할 수 있을까??

`getter`와 `setter`를 실제 프로퍼티 값을 감싸는 래퍼로 사용하여, 프로퍼티 값을 통제하는 방법이 있다

```javascript
let user = {
  get name() {
    return this._name;
  },

  set name(value) {
    if (value.length < 4) {
      alert("Too Short");
      return;
    }
    this._name = value;
  }
};

user.name = "Yoon";
alert(user.name); // Yoon

user.name = ""; // 너무 짧은 이름을 할당하려 함
```

`user`의 이름이 `_name`에 저장되어, 프로퍼티에 접근하는것이 `getter` 와 `setter`로 이루어진다.

> 솔직히 왜 사용하는지 이해는 되지 않지만, 그냥 이런게 있구나~ 하고 넘어가고 이후에 다시 확인해보기로 하자