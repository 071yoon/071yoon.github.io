---
layout: post
title: NEXT-Built-In CSS
subtitle: NEXT CSS 는 어떻게 사용하는가
categories: next.js
tags: [next.js, javascript, web, study]
---

## Global Stylesheet

프로젝트에 `stylesheet`를 추가하려면, `pages/_app.js` 라는 CSS 파일을 import 해야된다.

우선 다음과 같이 `style.css`를 만들었다 보면

```css
body {
  font-family: sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

이제 실제 page 에서 import 하면 된다.

```javascript
import '../style.css'

export default function MyApp({ Component, pageProps }) {
	return <Component {...pageProps}> />
}
```

그럼 스타일이 위에서 선언한 `style.css` 처럼 적용이 된다.

| global한 문제가 있기에 오직 하나만 import 해야 된다! 여기서는 `pages/_app.js`

## Import styles from `node_modules`

Next.js 9.5.4 버전부터는 `node_modules`에 CSS 파일을 넣는것이 가능해졌다. 만약 `bootstrap`이나 `nprogress` 같은 global stylesheet를 사용한다면, `pages/_app.js` 에서 import를 미리 해두어야 한다.

`Third Party` 컴포넌트를 사용한다면, 다음과 같이 정의 할 수 있다.

| Third Party란 프로그래밍시 도와주는 플러그인 혹은 라이브러리를 만드는 회사

```javascript
// components/ExampleDialog.js
import { useState } from "react";
import { Dialog } from "@reach/dialog";
import VisuallyHidden from "@reach/visually-hidden";
import "@reach/dialog/styles.css";

function ExampleDialog(props) {
  const [showDialog, setShowDialog] = useState(false);
  const open = () => setShowDialog(true);
  const close = () => setShowDialog(false);

  return (
    <div>
      <button onClick={open}>Open Dialog</button>
      <Dialog isOpen={showDialog} onDismiss={close}>
        <button className="close-button" onClick={close}>
          <VisuallyHidden>Close</VisuallyHidden>
          <span aria-hidden>×</span>
        </button>
        <p>Hello there. I am a dialog</p>
      </Dialog>
    </div>
  );
}
```

## Component-Level CSS

Next.js는 `[name].module.css` 이라는 파일 컨벤션을 이용하여 CSS Module을 공식적으로 지원한다. CSS 모듈은 자동으로 unique class 이름을 만들어 생성하며, 이는 충돌을 방지하기 위함이다. 즉 이런식으로 사용하면, component-level 에서 사용하기 더욱 쉬워진다. 그리고 이러한 CSS Module은 파일 어디에서 import 되어도 사용 가능해진다!

예를들어, 재사용 가능한 `Button` 이라는 컴포넌트가 `components/`안에 있다 가정하고 다음을 보자.

우선 `components/Button.module.css`를 만든다.

```css
.error {
  color: white;
  background-color: red;
}
```

후, `components/Button.js`를 만들어, 방금 제작한 CSS를 import 하면 다음과 같다.

```javascript
import styles from "./Button.module.css";

export function Button() {
  return (
    <button
      type="button"
      // Note how the "error" class is accessed as a property on the imported
      // `styles` object.
      className={styles.error}
    >
      Destroy
    </button>
  );
}
```

이러한 CSS Module을 사용하는것은 추가적인 기능이고, 편히 global CSS를 사용하여도 무방하다.

정적 build시에, 이러한 모든 CSS Module 들이 자동으로 분할되어지는데, 최종적으로 프로그램 딴에서 css 파일의 무게를 줄이기 위함이다.

## Sass Support

Next.js는 Sass도 물론 지원한다. `.scss` 와 `.sass` 둘 다 지원하며, 마찬가지로 모듈화를 하여 `.module.scss` 와 `.module.sass` 로 사용할 수 있다.

간단한 Sass의 예제로 보면 다음과 같다.

```css
/* variables.module.scss */
$primary-color: #64FF00

:export {
  primarycolor: $primary-color;
}
```

```javascript
// pages/_app.js
import variables from "../styles/variables.module.scss";

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout color={variables.primaryColor}>
      <Component {...pageProps} />
    </Layout>
  );
}
```

## CSS in JS

내가 주로 사용하는 방식인 CSS-in-JS 방식이다.

우선 사용하려면 `styled-jsx` 파일을 번들해야하며, 목적은 `shadow CSS`를 지원하여 마치 웹 컴포넌트처럼 사용하기 위함이다. 아쉽게도 SSR를 지원하지 않고, JS만 가능하다.

`styled jsx`의 예제는 다음과 같다.

```javascript
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  );
}

export default HelloWorld;
```
