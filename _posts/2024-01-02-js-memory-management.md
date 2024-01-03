---
layout: post
title: 자바스크립트 메모리관리
subtitle: 자바스크립트는 어떻게 메모리 관리를 하는가
categories: javascript
tags: [javascript, til]
---

## 메모리란

우선 간단하게 메모리 관리에 대한 이야기를 해보자.

메모리란 개념은 컴퓨터 공학을 처음 배울 때 부터 배우기 시작하여 끝도 없이 공부하며, 아직까지도 많은 프로그래머들을 괴롭힌다. C언어 같은 언어는 allocation 함수들(`malloc()`, `calloc()` 등)을 활용하여 동적으로 메모리를 사용하고 `free()`함수를 활용하여 메모리를 해제한다. 하지만, JS 혹은 Java같은 언어는 runtime에 객체가 생성 시 자동으로 메모리를 할당하고 필요하지 않을 때 해제하게 된다. 여기서 `필요하지 않을 때`의 시점이 컴퓨터가 판단하기에, 개발자가 메모리 관리에 대한 고민을 할 필요는 없지만 오히려 관리를 하지 않아 문제를 발생시킬 수 있다는 점에 유의해야 한다. 이를 더 깊이 이해하기 위해 `Javascript`의 메모리 관리에 대해 공부해보자.

## 메모리의 생존주기

메모리의 생존주기는 `할당 -> 사용 -> 해제`로 매우 간단하다.

여기서 `사용` 부분은 모든 언어에서 명시적으로 사용하지만, 앞서 말했던 것 처럼 `할당`과 `해제`는 Javascript와 같은 고수준 언어에서는 암묵적으로 작동한다.

### 할당

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

### 사용

일반적으로 js에서 variable을 가지고 읽고 쓰는것을 의미한다.

### 해제

`Javascript`가 `필요하지 않다`고 판단이 되면, 메모리를 해제한다. Python, Java, Javascript와 같은 언어들은 각 언어만의 Garbage Collection(GC)이 동작하며 메모리 관리를 하게된다. GC의 목적은 할당된 메모리들을 추적하여 더 이상 필요하지 않을 떄 회수하는 것이지만, 이를 판단하는것이 비결정적이기 때문에 우리도 GC에 대한 이해가 필요하다.

## Garbage Collection

Garbage Collection은 주로 두가지의 알고리즘을 사용한다. (단순 JS에 국한되는게 아님)

### Reference Counting

정말 간단한 방법으로, 아무도 해당 객체를 참조하고 있지 않을 때 필요없다고 간주되어 해제해 버리는 방법이다.

간단한 예시와 함께 살펴보자.

> 원래 js는 object에 저장될 때 obj의 주소값으로 저장하고, 해당 obj에서 변수에 대한 값 또한 주소로 저장하지만, 간편하게 도식화 하였다.

```javascript
let x = {
  a: {
    b: 2,
  },
};

let y = x;
```

2개의 객체가 생성되어, 변수 x에 저장되고, y또한 x를 참조하고 있으므로 가비지는 없다는게 명확하다.
![rc1](https://github.com/071yoon/071yoon.github.io/assets/66371206/6ba565c5-976b-49c3-93fc-74af9ba1f4ea)

```javascript
x = 1;
```

x에 대한 값을 immutable한 값으로 바꾸며 원래 객체에 대한 참조를 y만 하게 되었다
![rc2](https://github.com/071yoon/071yoon.github.io/assets/66371206/ef8edbaf-6330-4854-bc2d-abf9dfa27ba1)

```javascript
let z = y.a;

y = "mozilla";
```

y의 값을 immutable한 값으로 바꾸고, z의 값을 a를 참조하도록 수정하였으므로, 아직 객체의 a 속성이 z에 대해 참조되어 메모리를 해제할 수 없다.
![rc3](https://github.com/071yoon/071yoon.github.io/assets/66371206/8b3b5d4c-8e8a-4fff-8fe1-f95ffa4a2658)

```javascript
z = null;
```

이제 원래 x가 참조했던 객체의 모든 속성에, 참조하고 있는 변수가 없으므로 가비지 콜렉션이 수행되어, 메모리의 해제가 이루어진다.
![image](https://github.com/071yoon/071yoon.github.io/assets/66371206/2d606e85-02d6-495a-89d9-3439be9e1509)

하지만, 이런 단순한 방법에는 서로 참조하는 `순환 참조`가 이루어질 때 문제가 생긴다. 서로 참조하는 속성이 있는 경우, 함수 호출이 끝났음에도, 서로 참조하고 있기에 메모리가 해제되지 않고 메모리 누수가 일어날 수 있다. 순환참조의 예시는 다음과 같다.

```javascript
function f() {
  const x = {};
  const y = {};
  x.a = y; // x -> y 참조
  y.a = x; // y -> x 참조

  return "nothing";
}
```

### Mark and Sweep

해당 방법은 `필요 없는 객체` 를 `도달할 수 없는 객체`로 인식한다.

우선 `roots`라는 객체 시작하여, roots를 참조하는 객체들을 찾으며, 도달할 수 없는 객체들을 탐색 및 수집하는 방식이다.

> js는 전역 객체

이러한 방식은, 이전처럼 `순환 참조`가 일어나더라도 도달할 수 없음을 판단하고, 메모리를 회수할 수 있고, 실제로 js의 최신 엔진은 해당 방법으로 메모리 관리를 하게된다.

단점으로는, garbage collection을 수동으로 조작할 수 없고, 공식적으로 코어 언어에서도 지원하지 않을 예정이라 한다.

## v8 엔진의 메모리 구조

v8 메모리 구조는 우선, 메모리의 Resident Set이라는 공간에서 시작된다. 해당 영역에서 스택과 힙 공간으로 나누어져 메모리를 할당하게 된다. Operating System에서 배웠듯, Stack 공간은 함수호출이 끝난 후 삭제되지만, 동적인 힙 메모리는 그렇지 않기에, 메모리 누수에 취약하며 주의깊게 봐야한다.

![v8-memory-structure](https://github.com/071yoon/071yoon.github.io/assets/66371206/914c8119-239e-4d13-adc0-1662ead4b693)

힙 공간은 위 그림에서 Stack을 제외한 모든 부분이며, 여러 공간들로 나누어져 있지만 실제로, GC가 일어나는 New Space와 Old Space를 주의깊게 보자.

- New Space: 새로 만들어지는 Object가 할당
- Old Space: New Space에서 Minor GC가 2번 일어날 동안 살아남는 경우 저장된다

  - Old Pointer Space: 다른 객체를 참조하는 객체 (다른 객체에 대한 포인터를 가지는 경우)
  - Data Space: 문자열/실수 등 데이터만 가지고 있는 객체

- Large Object Space: mmap 메모리를 가지는 객체들이 있으며, GC의 대상이 아니다.
- Code space: JIT 컴파일러가 컴파일된 코드들을 저장하여, 실행 가능한 메모리가 있는 영역이다.
- Rest: 이 영역들은 각각 Cells, PropertyCells, Maps이며, 각 영역은 모두 같은 크기의 객체들을 포함하며, 어떤 종류의 객체를 참조하는지에 대한 제약이 있어서 수집을 단순하게 만든다.

## v8 엔진의 GC

v8 엔진의 GC는 Java의 GC와 사뭇 비슷하다.

> Java에서 갓 생성된 객체를 Eden이라고 따로 관리했던 것을 제외하면 매우 유사하다고 생각함

JS의 Garbage Collection은 `Generational Hypothesis` 이론을 중점으로 이루어지는데, 이는 새로운 객체가 오래된 객체보다 쓸모 없어질 가능성이 높다, 즉 일회성 데이터들이 많다는 이론에 의거하여 동작한다.

그렇게 Garbage Collection을 Minor GC와 Major GC 둘로 나누어 관리하게 된다.

### Minor GC

Minor GC는 New Space의 Garbage를 수집하는 역할을 한다. 앞서 다이어그램에서 보았듯, New Space에는 두개의 Semi Space가 존재한다. 여기서 한 Semi Space는 항상 비워두며, Mark and Sweep 알고리즘을 통해 삭제되지 않는 객체들을 옮기고, 다시 한 Space를 비우는 방식으로 작동한다. 만약 다음 Minor GC에도 해당 객체들이 살아있다면, 이들은 Old Space로 이동하게 된다.

이를 간단히 도식화 하면 다음과 같다.

![minor_gc](https://github.com/071yoon/071yoon.github.io/assets/66371206/cfcfe251-94d4-4563-a570-a62f5f2204b7)

### Major GC

이제 Old Space에서의 Garbage Collection을 알아보자. Major GC는 Mark and Sweep Compact 와 Tri-Color Algorithm을 사용하며, 크게 마킹 -> 스위핑 -> 압축 으로 이루어진다.

1. 마킹

   우선 어떤 객체들이 할당 해제가 되어야 되는 객체들인지 판단을 하기 위해, 전역객체부터 시작하여 DFS로 순회하며 Tri Color Algorithm으로 마킹을 한다.

   Tri Color는 3가지 색으로 node를 나타내며

   - white: 아직 탐색을 하지 못한 노드
   - gray: 탐색을 했지만, 객체가 참조하고 있는 노드까지는 탐색을 완료하지 못한 노드
   - black: 참조하고 있는 개체까지 확인을 완료한 노드

   해당 알고리즘을 마치고 나면, 모든 노드들은 white / black인 형태로 남게 되어, white인 경우 도달할 수 없는 노드로 판단할 수 있다.

2. 스위핑

   white 인 노드들은 도달할 수 없으므로, 필요없는 객체라 판단되어 할당된 메모리 공간을 해제한다.

3. 압축

   메모리 단편화가 심한 페이지들을 재배치하여 메모리 공간을 확보한다. 이는 Fragmentation과 Segmentation을 검색해보면 더 자세히 알 수 있다.

## Orinoco

Garbage Collection을 수행하는 동안에는 Stop The World가 일어나며, 모든 프로세스가 멈추어 UX 적으로 많은 문제가 생긴다. 이를 v8 엔진은 우아하게 처리하기 위한 Orinoco 프로젝트를 알아보자.

### Incremental Marking

2011년에 나온 stop the world의 해결방안으로 나온 incremental marking 이다.
![incremental marking](https://github.com/071yoon/071yoon.github.io/assets/66371206/4544a7bf-de1e-44d1-ae0c-4a3a7eb0647e)

다이어그램에서 보이듯, 짧은 GC로 쪼개어 프로그램의 반응속도 및 UX를 개선하려함을 볼 수 있다. 하지만, 프로그램이 동작함에 따라, object graph에 대한 변동사항이 생길 때 마다 Garbage Collection에 알려야 하기 때문에, idle time이 없는 경우 cost가 굉장히 높다.

여기서 idle time이란, 크롬 자체에서 프로그램이 쉬는 기간을 미리 알 수 있다는 것이다. 이를 활용하면, 예를 들어, 1 프레임에 필요한 연산을 16ms안에 해결한다면, 남는 시간동안 idle GC를 활용하여, GC를 미리 할 수 있다.

### Parallel & Concurrent

해당 방식은, Helper Thread를 두어, 병렬 혹은 동시적으로 Garbage Collection을 수행하여 stop the world를 최소한을 줄이는 방식이다.

그래서 모든 GC의 기술들을 합치면 현재는 이러한 형태를 띄게 된다.

![v8-gc-graph](https://github.com/071yoon/071yoon.github.io/assets/66371206/fd27a383-cf71-4110-bc83-d044750b76e5)

## 참조

[mozilla](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_management)

[memory-management-in-v8](https://deepu.tech/memory-management-in-v8/)

[v8-dev-trash-talk](https://v8.dev/blog/trash-talk)

[v8-concurrent-marking](https://v8.dev/blog/concurrent-marking)
