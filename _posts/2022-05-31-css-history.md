---
layout: post
title: CSS의 역사
subtitle: CSS의 역사와 트렌드를 알아보자
categories: CSS
tags: [CSS, SASS, SCSS, styled-components, CSS-Moduler]
---

<center>CSS 트렌드</center>

![image](https://user-images.githubusercontent.com/66371206/171093573-f5758d9a-6ff3-44e8-aa86-8c52ad983c93.png)

<center>(출처: oliverturner's twitter)</center>

## Traditional CSS

가장 전통적인 CSS 방식에는 크게 3가지 방식이 존재한다.

- [Inline CSS](#inline-css)
- [Internal CSS](#internal-css)
- [External CSS](#external-css)

### Inline CSS

> 일반적으로 간단하게 태그안에 style을 적용하는 방법

```html
<h1 style="color:white;padding:30px;">Inline CSS</h1>
<p style="color:white;">HTML 안에서 직접 스타일 적용.</p>
```

#### Inline CSS 장점

- HTML 페이지에 쉽고 빠르게 CSS 룰을 삽입할 수 있다.
  - 변경에 대한 미리보기, 그리고 빠르게 고칠 수 있는 성능에 유용하다.
- 별도의 문서로 외부 스타일을 만들거나 업로드 할 필요가 없다.

#### Inline CSS 단점

- CSS룰을 모든 HTML 엘리먼트에 추가하는 것은 시간 낭비이며 HTML 구조를 복잡하게 만든다.
- 여러 엘리먼트에 스타일을 적용하는 것은 웹페이지 사이즈가 커지고 다운로드 시간이 걸리게 된다.

### Internal CSS

> CSS Object Model처럼 사용하는 방법.

```html
<style type="text/css">
  body {
    background-color: blue;
  }
  h1 {
    color: red;
    padding: 60px;
  }
</style>
```

#### Internal CSS 장점

- 스타일시트에 클래스와 ID 선택자를 사용할 수 있다.
- 동일한 HTML 파일 내에서만 코드를 추가하여 여러 파일을 업로드할 필요가 없다.

#### Internal CSS 단점

- HTML 문서에 코드를 추가하는 것은 페이지의 사이즈와 로딩 시간을 증가시킬 수 있다.

### External CSS

> 외부에 .css 파일을 따로 빼서 만드는 방법.

```css
/* external.css */
.xleftcol {
  float: left;
  width: 33%;
  background: #809900;
}
.xmiddlecol {
  float: left;
  width: 34%;
  background: #eff2df;
}
```

```html
<!-- head -->
<link rel="stylesheet" type="text/css" href="external.css" />
```

#### External CSS의 장점

- CSS 코드가 별도의 문서로 있기 때문에 HTML 파일 구조가 깔끔해지고, 사이즈가 작아진다.
- 여러 페이지들에 같은 .css 파일을 사용할 수 있다.

#### External CSS의 단점

- 외부 CSS 파일이 로드되기 전까지 페이지가 올바르게 표시되지 않는다.
- 여러 CSS 파일을 업로드 하거나 연결하면 사이트 다운로드 시간이 증가할 수 있다.

## SASS/SCSS

> SCSS와 SASS는 CSS를 편리하게 이용할 수 있도록 도와주며 추가 기능도 있는 확장판이다

간단하게 css 스타일의 코드 차이를 보며 이해해보자

### 일반적인 CSS

```css
.list {
  width: 100px;
  float: left;
}
li {
  color: red;
  background: url("./image.jpg");
}
li:last-child {
  margin-right: -10px;
}
```

### SCSS

```scss
.list {
  width: 100px;
  float: left;
  li {
    color: red;
    background: url("./image.jpg");
    &:last-child {
      margin-right: -10px;
    }
  }
}
```

### SASS

```sass
.list
  width: 100px
  float: left
  li
    color: red
    background: url("./image.jpg")
    &:last-child
      margin-right: -10px
```

### 왜 쓰는가

> CSS가 복잡한 언어는 아니지만 작업이 크고 고도화 될수록 불편하다!

SCSS나 SASS는 선택자를 많이 남발하거나 프로젝트가 커지면서 점점 복잡해지게 되는데, 이러한 부분에서 가독성과 재사용성을 높여주며 유지보수를 쉽게 만들어준다. 여기서 도움을 주는 도구는 다음과 같다.

- 변수의 사용
- 조건문과 반복문
- Import
- Nesting
- Mixin
- Extend/Inheritance

### SCSS vs SASS

SCSS = (Sassy CSS) -> 지리는 CSS

SASS = (Syntactically Awesome Style Sheets) -> 문법적으로 엄청난 CSS

> SASS는 들여쓰기 기반, SCSS는 {} 기반

일반적으로 SCSS가 더 넓은 범용성과 CSS의 호환성 등의 장점으로 SCSS를 사용하기를 권장하고 있다!

## BEM

> Blcok, Element, Modifier

```css
.header__navigation--navi-text {
  color: red;
}
```

여기서 header는 `Block`, naviagtion은 `Element`, navi-text는 `Modifier가` 된다.

BEM은 기본적으로 ID를 사용하지 않으며, class만을 사용하며, 이름을 연결할 때는 block-name과 같이 하이픈 하나만 써서 연결한다.

### Block

> 재사용 가능한 기능적으로 독립적인 페이지 컴포넌트

![9977B24F5C7BF67802](https://user-images.githubusercontent.com/66371206/171098198-40127e9b-c070-4c13-8a33-4198472d5d28.png)

즉 위 사진처럼 떼어내서, 재사용이 가능한 컴포넌트를 부른다. 어디에서나 종속되지 않고, 독립적으로 사용할 수 있으며 특징으로는 블럭은 블럭으로 감쌀 수 있다는 점이 있다.

### Element

> 블럭을 구성하는 단위

![993FCF4A5C7BF68F27](https://user-images.githubusercontent.com/66371206/171098520-fe2f8dac-867e-45e3-85d3-9979e60e5647.png)

자신이 속한 블럭 내에서만 의미를 가지기 때문에 블럭 안에서 떼어다 다른 데 쓸 수 없다.

```javascript
<form class="search-form">
  <input class="search-form__input" />
  <button class="search-form__button">Search</button>
</form>
```

위 예시에서 `.search-form`은 블럭이고, `.search-form__input`과 `.search-form__button`은 엘리먼트이다.
저 search-form이란 블럭은 떼어내서 마음껏 붙여도 된다.
하지만 내부의 input과 button은 검색을 위한 인풋창이자 버튼이기 때문에, search-form 안에서만 존재 의미가 있는 엘리먼트이다.

### Modifier

> 블럭이나 엘리먼트의 속성을 담당

![992F5E4A5C7BF68F33](https://user-images.githubusercontent.com/66371206/171098735-bb4930fb-7ee3-4bfd-82e5-de42bd017a50.png)

```javascript
<ul class="tab">
  <li class="tab__item tab__item--focused">탭 1</li>
  <li class="tab__item">탭 2</li>
  <li class="tab__item">탭 3</li>
</ul>
```

위 코드에서 --focused가 수식어에 해당한다. 저렇게 작성된 걸 불리언(boolean) 타입이라고 하는데, 그 값이 true라고 가정하고 사용하며, `key-value`로도 사용할 수 있다.

## CSS Module

> 리액트 프로젝트에서 컴포넌트를 스타일링 할 때 CSS Module 이라는 기술을 사용하면, CSS 클래스가 중첩되는 것을 완벽히 방지할 수 있다.

```css
/*box.css*/
.Box {
  background: black;
  color: white;
  padding: 2rem;
}
```

```javascript
//box.js
import React from "react";
import styles from "./Box.module.css";

function Box() {
  return <div className={styles.Box}>{styles.Box}</div>;
}

export default Box;
```

그럼 다른 css 파일에서 동일한 css className을 만들어야 된다는 부담감에 필요없이 만들 수 있다.

## CSS in JS

> 마지막으로 CSS in JS에서 제일 유명한 styled components 를 보며 알아보자

```javascript
// Box4.js
import React from "react";
import styled from "styled-components";

export default function Box({ size }) {
  const isBig = size === "big";
  const label = isBig ? "큰 박스" : "작은 박스";
  return <BoxCommon isBig={isBig}>{label}</BoxCommon>;
}

const BoxCommon = styled.div`
  width: ${(props) => (props.isBig ? 200 : 100)}px;
  height: 50px;
  background-color: #aaaaaa;
`;
```

다음과 같이 진행했던 CSS를 module 처럼 뺄 필요도 없이, 한 페이지에서 관리할 수 있다. 즉 CSS코드를 자바스크립트 파일안에서 하므로, 내부응집도가 올라가고, 동적으로 CSS를 변경하기도 쉽다.

출처

<https://latte-is-horse.tistory.com/38>

<https://velog.io/@jch9537/CSS-SCSS-SASS>

<https://nykim.work/15>

<https://react.vlpt.us/styling/02-css-module.html>
