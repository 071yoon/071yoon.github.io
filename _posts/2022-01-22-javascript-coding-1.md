---
layout: post
title: 바닐라JS로 앱 만들기 1
subtitle: 자바스크립트 프로젝트 기초에대해서 배워보자
categories: javascript
tags: [javascript, TIL, web]
---

## 자바스크립트 실습

---

자바스크립트의 이론적인 부분에 대해서는 공부를 대충 해봤지만, 실생활에 어떤식으로 사용할 지, 혹은 어떤식으로 프로젝트를 진행할 지에 대해서 너무 무지했기 떄문에, 처음에는 강의에 의존을 해보기로 결정했다. 여러 자바스크립트 강의를 찾던 도중, `노마드코더`가 자바스크립트를 처음 접하기 좋다는 소식을 들어 이 사이트에서, 바닐라JS로 크롬 앱 만들기 강의를 시작해보기로 결정했다.

### Why Javscript?

---

왜 우리는 자바스크립트를 사용할까??

사실 자바스크립트는 그렇게 좋은 언어는 아니다. `let`, `var` 등등 (요새는 `var`를 안쓴다고 하긴 하더라) 데이터 타입을 명시해주는것 부터, 문법등을 보면 그렇게 멋진 언어는 아니다. 개인적으로 C언어가 완벽에 가까운 언어라고 생각을 하는데 (개발자의 역량에 따라 시간을 투자하면 원하는만큼 다 구현이 가능하니까) 왜 자바스크립트를 사용할까?? 사실 어쩔 수 없다는게 맞는 표현이다. 문제는 처음부터 웹을 만들 떄 자바스크립트를 사용했다는 것...! 애초부터 급조한 언어였기 때문에, 문제가 많았다.

> 실제로 10일만에 만든 언어라고 한다!

하지만 이미 많은 사람들이 웹에서 자바스크립트를 사용하였기에, 갑자기 언어를 바꿔버리면 웹에 마비가 올 수 있다는 생각에 그냥 해당 자바스크립트를 두고 패치를 계속 진행을 하며, 현재의 자바스크립트까지 왔다고 한다. 즉 자바스크립트는 현재 웹에서 `대체불가언어` 라고 해도 과언이 아닐정도로 모든 웹에서 사용중이며, 우리가 배워야 하는 이유이다. 너무 싫어서 사용하기 싫다고 하더라도 웹쪽으로 커리어를 생각중이라면 어쩔 수 없이 배워야 하는 언어..? 정말 애증의 언어가 아닐 수 없다. 물론 요즘은 그런점들을 보완하며, 같이 상용이 가능한 `typescript`의 존재로 이러한 원성들을 조금이나마 잠재워 주고 있는듯 보인다.

### Project의 시작

---

옛날에 웹디자인을 공부 할 때, 해봤지만 너무 옛날이라 기억도 안나고 역시 정리를 안해두니 찾아보기가 힘들어 이번에는 조금 정리를 하면서 진행을 해보려고 한다.

우선 웹을 배우려면 `HTML`, `CSS`, `Javascript` 이 3개의 연관성에 대해서 조금이나마 알고 가야한다. `CSS` 와 `Javascript` 는 일반적으로 혼자 작동할 수 없는 언어이다. 즉 `HTML의 의존성을 가진 파일들` 이라고 할 수 있다. 아무리 이쁜 디자인을 `CSS` 에서 진행을 하고 얼마나 멋진 함수들을 `Javscript` 에서 구현을 했다 한들, 실제 보여지지않으면 의미가 없고, 이러한것들을 가시적으로 보여주면서 동작하게하는 것이 `HTML`이라고 보면 되겠다. 어떻게 연결하는지 기본적인 HTML을 보며 살펴보자

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="style.css" />
    <title>momentum</title>
  </head>
  <body>
    <script src="script.js"></script>
  </body>
</html>
```

이런식으로 `stylesheet`를 `style.css`로 두고 `script`를 `script.js` 로 두어 링크를 했다고 보면 되겠다.

> 주의할점! `script.js` 는 `body`의 끝에 주로 둔다. WHY? 왜냐하면 보통 사이즈가 큰 파일들은 `script.js`에 들어가 있고, 인터넷 속도가 느려서 자바스크립트파일을 다운받는데 오래 걸린다면, 그 전에 화면에 출력할 것을 먼저 다운받지 못해 빈 화면을 출력하고, 자바스크립트를 다운 다 받은 후 출력을 하기때문. 즉 유저의 편의를 좀 봐주자

### Data Types

---

일단 자바스크립트는 `syntax`가 세개밖에 없다. `const, let, var` 심지어 `var` 는 잘 쓰지 않으므로 `const` 와 `let` 두개가 있다고 해도 무방하다.

> 심지어 파이썬은 저것조차 안써도 된다

그럼 데이터타입을 알아서 정해준다는 것이다. 그럼 문제가 무엇이냐?? 언제 정수에서 문자형으로 바뀌는것이고, 문자열로 바뀌는지 프로그램이 정해주기 때문에 정할 수 없다는 것이다. 그래서 새로운 `typescript` 라고 자바스크립트에서 `type` 형식을 지정해주는 변형된 언어가 탄생을 했다.

그럼 요새 사용은 안하는 `var` 형식은 언제 사용하는것일까? 사실 사용을 잘 안한다. `var` 는 처음에 만들어진 변수 선언 type 이고, 어디서나 재정의가 가능하다는 장점이 있지만, 사실이건 매우 위험하고 차라리 다른 변수명을 선언하는게 훨씬 낫다. 그래서 요새는 `let` 과 `const`로 대체해서 사용한다.

자바스크립트에서 특이한것이 있는데, `null` 과 `undefined` 가 두개나 존재한다는 것이다. `null`은 내가 지정을 해 줘서 비어있을 때 `null` 인것이고, `undefined`는 내가 만약 `let variable;` 을 해주고 지정을 안해주면 `undefined` 라고 나오게 된다.

> 즉 null 은 비어있는것이고, undefined는 변수이지만 값이 없는것

그리고 자바스크립트 배열에는 C 혹은 C++ 과 다른 특이점이 있다. 나의 테스트케이스를 보자

```javascript
const test = ["test", 30, false, "c"];
for (let i = 0; i < 4; i++) {
  console.log(typeof test[i] + "\n");
}
test[7] = "test2";
for (let i = 0; i < test.length; i++) {
  console.log(i + ": " + typeof test[i] + "\n");
}
```

> 특이한 점 1 : 배열의 type 은 각 항목마다 다르다

> 특이한 점 2 : 만약에 7번을 만들면 자동으로 배열의 크기가 8로 늘어나고, 5 6 7 번은 undefined로 정의된다

### 비교연산자

---

타 언어들을 보면 비교연산자로 `==` 밖에 쓰지 않는데, 자바스크립트는 특이하게 `===` 를 주로 사용한다. 그렇다고해서 `==` 가 없다는 것은 아니다. 차이점은 `==` 은 값만 비교하는 것이고 `===` 는 `type` 까지 묶어서 비교를 한다는 점이다. 즉 `5 == '5'` 는 참이지만 `5 === '5'` 는 거짓이라는 것.
