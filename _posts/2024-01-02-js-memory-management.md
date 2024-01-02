---
layout: post
title: 자바스크립트 메모리관리
subtitle: 자바스크립트는 어떻게 메모리 관리를 하는가
categories: javascript
tags: [javascript, til]
---

# 메모리란

우선 간단하게 메모리 관리에 대한 이야기를 해보자.

메모리란 개념은 컴퓨터 공학을 처음 배울 때 부터 배우기 시작하여 끝도 없이 공부하며, 아직까지도 많은 프로그래머들을 괴롭힌다. C언어 같은 언어는 allocation 함수들(`malloc()`, `calloc()` 등)을 활용하여 동적으로 메모리를 사용하고 `free()`함수를 활용하여 메모리를 해제한다. 하지만, JS 혹은 Java같은 언어는 runtime에 객체가 생성 시 자동으로 메모리를 할당하고 필요하지 않을 때 해제하게 된다. 여기서 `필요하지 않을 때`의 시점이 컴퓨터가 판단하기에, 개발자가 메모리 관리에 대한 고민을 할 필요는 없지만 오히려 관리를 하지 않아 문제를 발생시킬 수 있다는 점에 유의해야 한다. 이를 더 깊이 이해하기 위해 `Javascript`의 메모리 관리에 대해 공부해보자.

# 메모리의 생존주기

메모리의 생존주기는 `할당 -> 사용 -> 해제`로 매우 간단하다.

여기서 `사용` 부분은 모든 언어에서 명시적으로 사용하지만, 앞서 말했던 것 처럼 `할당`과 `해제`는 Javascript와 같은 고수준 언어에서는 암묵적으로 작동한다.

## 할당

아래는 다양한 js의 메모리 할당방법들이다.

```javascript
const o = {
  a: 1,
  b: null,
}; // object가 아닌 literal도 비슷하게 할당할 수 있다

// 함수식 또한 오브젝트를 담기 위한 메모리를 할당한다.
someElement.addEventListener(
  "click",
  () => {
    someElement.style.backgroundColor = "blue";
  },
  false
);

const d = new Date(); // new를 활용해 새로운 객체 할당

const e = document.createElement("div"); // DOM 엘리먼트를 위해 메모리를 할당

const s = "azerty";
const s2 = s.substr(0, 3); // 문자열은 immutable하기에, 새로운 메모리가 아닌 [0,3] 만 할당
```

## 사용

일반적으로 js에서 variable을 가지고 읽고 쓰는것을 의미한다.

## 해제

`Javascript`가 `필요하지 않다`고 판단이 되면, 메모리를 해제한다. Python, Java, Javascript와 같은 언어들은 각 언어만의 Garbage Collection(GC)이 동작하며 메모리 관리를 하게된다. GC의 목적은 할당된 메모리들을 추적하여 더 이상 필요하지 않을 떄 회수하는 것이지만, 이를 판단하는것이 비결정적이기 때문에 우리도 GC에 대한 이해가 필요하다.

# Garbage Collection

Garbage Collection은 주로 두가지의 알고리즘을 사용한다. (단순 JS에 국한되는게 아님)

## Reference Counting

정말 간단한 방법으로, 아무도 해당 객체를 참조하고 있지 않을 때 필요없다고 간주되어 해제해 버리는 방법이다.

```javascript
let x = {
  a: {
    b: 2,
  },
};
// 2개의 객체가 생성되었습니다. 하나의 객체는 다른 객체의 속성으로 참조됩니다.
// 나머지 하나는 'x' 변수에 할당되어 참조됩니다.
// 명백하게, 가비지 콜렉션 수행될 메모리는 하나도 없습니다.

let y = x;
// 'y' 변수는 위의 객체를 참조하는 두 번째 변수입니다.

x = 1;
// 이제 'y' 변수가 위의 객체를 참조하는 유일한 변수가 되었습니다.

let z = y.a;
// 위의 객체의 'a' 속성을 참조했습니다.
// 이제 이 객체는 두 개의 참조를 가집니다.
// 'y'가 속성으로 참조하고 'z'라는 변수가 참조합니다.

y = "mozilla";
// 이제 맨 처음 'x' 변수가 참조했던 객체에 대한 참조가 없기 때문에, 가비지 컬렉션이 가능합니다.
// 그러나, 객체의 'a' 속성이 여전히 'z' 변수에 의해 참조되므로
// 메모리를 해제할 수 없습니다.

z = null;
// 이제 맨 처음 'x' 변수가 참조했던 객체의 'a' 속성에는
// 참조가 없으므로 가비지 콜렉션이 수행될 수 있습니다.
```

## Mark and Sweep

### 참조

[mozilla](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_management)
[memory-management-in-v8](https://deepu.tech/memory-management-in-v8/)
