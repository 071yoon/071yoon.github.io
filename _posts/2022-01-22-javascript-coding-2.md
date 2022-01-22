---
layout: post
title: 바닐라JS로 앱 만들기 2
subtitle: 자바스크립트 프로젝트 제작을 시작해보자
categories: javascript
tags: [javascript, TIL]
---

## 자바스크립트를 Browser로 로딩하기

---

### `document` Object

`console`에 `document`를 쓰면, 이미 정의되어 있는 함수인걸 알 수 있다.
그럼 html 에서 `block`으로 지정한 부분을 자바스크립트에서 바로 가져올 수 있다.

> ex) `document.title` -> 내가 지정한 HTML 의 title

즉 이렇게 하면 HTML 의 코드를 자바스크립트의 관점에서 볼 수 있게된다
