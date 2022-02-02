---
layout: post
title: 바닐라JS로 앱 만들기 2
subtitle: 자바스크립트 프로젝트 제작을 시작해보자
categories: javascript
tags: [javascript, TIL, web]
---

## 자바스크립트를 Browser로 로딩하기

---

### `document` Object

`console`에 `document`를 쓰면, 이미 정의되어 있는 함수인걸 알 수 있다.
그럼 html 에서 `block`으로 지정한 부분을 자바스크립트에서 바로 가져올 수 있다.

> ex) `document.title` -> 내가 지정한 HTML 의 title

즉 이렇게 하면 HTML 의 코드를 자바스크립트의 관점에서 볼 수 있게된다

그럼 다른 block 에서 `id`로 가져오려면 어떻게 해야될까??

HTML 에서 `<h1 id="name">Name</h1>` 이런식의 블럭이 있다면 간단하게 `document.getElementById("name")` 이런식으로 호출 할 수 있다.

> 주의할점! `getElementById` 는 특정항목 하나만 가져오기 때문에 변수 하나에 저장되지만, `getElementByClass` 혹은 `getElementByTag` 는 여러 값들을 가져오기 때문에 배열로 할당되어 저장된다

하지만 가장 많이 사용하는 것은 `querySelector`라고 한다. 하나만 가져오고 가져오는것 중 하나를 CSS형태로 선택 할 수 있어서 가장 깔끔하고 보기 편하다고 한다. 그리고 만약 겹친다면 첫번 째 나오는 element 만 가져온다!!

- 배열로 가져오려면 `quertySelectorAll`을 사용하면된다.

  `document.querySelector('.class_name block')`

  `docuemnt.querySelector('.class_name block:first-child)` 도 가능하다

> `document.querySelector("#id")` 는 `document.getElementById("hello")` 와 동일하다

그럼 이렇게 호출된 `name` 은 `name.innerText()` 함수를 통해 안에 있는 내용을 변경 할 수 있게 해준다.

### Events

`variable.addEventListener("event", function);` 으로 선언을 해주면, 어떠한 event 를 할 때마다 function을 실행 해 줄것이다. event의 예로는 click, hover 기타등등이 있다. `console.dir` 으로 어떠한 함수들이 있는지 확인 할 수 있다.

> function을 호출할 떄 함수의 이름만 호출함에 주의! function()이렇게 아예 실행하도록 주지않고, 이름만 넘겨줘서 Event가 발생 시 function 을 실행하도록 한다

`HTMLHeadingElement` 에 들어가서 어떠한 API 들을 사용 할 수 있는지 확인을 해봐도 된다.

혹은 `variable.on'event' = function` 이런식으로 선언을 해도 같은식으로 동작하게 만들 수 있다

여기서 `Event`의 특징으로 또 강력한 것은 `Window` 객체에서 불러 올 수 있다는 것이다. `Window`는 현재 실행을 하는 그 화면을 말하는 것이고, 그 화면에서 정말 많은것을 구현 할 수 있다. 예를들어 `resize` 로 윈도우 크기를 조절하고 있을때의 이벤트, 혹은 `copy`, `offline` 등등 정말 많은 이벤트들이 구현되어 있고, 우리는 사용만 하면 된다!!

내가 만약 `class` 를 받아와서 사용중에 있는데, 현재 내가 사용하고 있는 클래스는 폰트지정 클래스이고, 색변환 클래스를 추가하고 싶으면 어떻게 사용해야 될까??

```javascript
function handleCheck() {
  const clickedClass = "clicked";
  if (h1.classList.contains(clickedClass)) {
    h1.classList.remove(clickedClass);
  } else {
    h1.classList.add(clickedClass);
  }
}
```

이런식으로 사용할 수 있다. 그럼 원래 있던 클래스 `class = "test"` 라 두면 추가되면 `class = "test clicked"` 이렇게 되고 remove시 `class = "test"` 이렇게 돌아갈 것이다. 이런식으로 클래스의 중첩이 가능하다!!

이걸 편하게 해주는것이 `toggle function` 은 어떤 역할이다.

```javascript
function handleCheck() {
  h1.classList.toggle("clicked");
}
```

이런식으로 한번에 해결 할 수 있다.

즉 toggle 은 이미 있는지 확인을 해서, 있다면 삭제하고 없다면 생성해주는 불을 키고 끄는것과 비슷한 행동을 해주는 함수라 할 수 있다.

다음 포스트부터는 실제로 코드를 작성해서 VanillaJS 앱을 만들어보겠다
