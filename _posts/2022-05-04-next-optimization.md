---
layout: post
title: NEXT-Optimization
subtitle: NEXT에서의 Optimization
categories: next.js
tags: [next.js, javascript, web, study]
---

## Image Component and Image Optimization

`next/image` 에서의 Next의 이미지 컴포넌트는 HTML에서 `<img>` 의 연장선이라 볼 수 있다. 더 좋은 Web 환경을 위해 더 좋은 효율과 압축성을 보여준다. 이러한 효율성은 웹사이트 사용에 있어 UX 적으로 도움이 된다.

> SEO 측면에서도 도움이 된다

이러한 효율성은 크게 4가지로

1. Improve Perforamce : 모든 장치마다 최신 이미지 포맷을 사용하여 정확한 사이즈를 전달한다

2. Visual Stability : CLS(Cumulative Layout Shift)를 방지한다

> CLS란 누적 레이아웃 변경으로, 웹페이지를 보는 사용자에게 예상치 못한 화면 변환이 나타나는 빈도 수

3. Faster Page Loads : 이미지가 `viewport`에 접근 시 로딩되어, 빠르고, 로딩 전 blur-up placeholder 를 설정 할 수 있다.

4. Asset Flexibility : 다른 서버에 이미지가 있는 경우에도, 원하는대로 이미지 리사이징이 자유롭다

### Local Images

이미지의 예제는 다음과 같다

```javascript
import Image from "next/image";
import profilePic from "../public/me.png";

function Home() {
  return (
    <>
      <h1>My Homepage</h1>
      <Image
        src={profilePic}
        alt="Picture of the author"
        // width={500} automatically provided
        // height={500} automatically provided
        // blurDataURL="data:..." automatically provided
        // placeholder="blur" // Optional blur-up while loading
      />
      <p>Welcome to my homepage!</p>
    </>
  );
}
```

`import` 하는 과정이 정적이 되어야 build 과정에서 분석이 되기에 `await import()` 혹은 `require()`같은 동적인 행동은 지원하지 않는다.

| width 와 height 가 자동으로 고정 된 이유는 CLS를 방지하기 위함이다

### Remote Images

외부에 있는 이미지를 사용하기 위해서는 `src`로 위치를 잘 가져와야한다. Next.js같은 경우 build 과정에서 외부로 접근 할 권한이 없으므로 아까 전과는 달리 `width`, `height`, `blurDataURL` 을 넣어 줘야한다.

```javascript
import Image from "next/image";

export default function Home() {
  return (
    <>
      <h1>My Homepage</h1>
      <Image
        src="/me.png"
        alt="Picture of the author"
        width={500}
        height={500}
      />
      <p>Welcome to my homepage!</p>
    </>
  );
}
```

### Domains

가끔 외부의 이미지를 사용하면서도 Next.js의 최적화 기법을 사용하고 싶을 수 있다. `loader`를 기본으로 두고, `src`에 절대경로 URL을 넣어 사용 할 수 있다.

어플을 외부로부터 보호하기 위해 아래와 같이 `next.config.js`파일을 사용하여 외부 도메인으로 바로 접근하는것을 막을 수 있다.

```javascript
module.export = {
  images: {
    domains: ["071yoon.github.io", "github.com/071yoon"],
  },
};
```

### Loaders

앞서 [Remote Images](#remote-images) 에서처럼 URL의 부분만 쓰는 경우 (`'/me.png'`) 는 `next/image` 의 loader의 구조상 가능한 부분이다.

`Loader`는 이미지의 URL을 만들어 주는 기능을 한다. 제공된 `src`에 root domain 까지 추가 후 image를 다른 사이즈로 여러 URL을 만들어 연결 요청을 보낸다. 이러한 URL들은 srcset을 만들어내고, 방문자는 각자 `viewport`에 맞는 사이즈의 이미지를 볼 수 있게 된다.

`Next.js`의 default loader는 `built-in Image Optimization API`를 사용했고, 웹의 어디에서나 이미지를 최적화 할 수 있었고, 바로 Next.js의 웹 서버에 보냈다. 만약 이미지를 CDN 이나 이미지 서버에 바로 보내고 싶다면, `built-in loader`를 사용하거나, 자바스크립트에서 따로 추가를 해야된다.

이러한 로더는 Application Level 에서 이미지당 하나씩 정의가 된다.

### Priority

이미지에 `priority`란 속성을 넣어 매 페이지마다 LCP element를 가지게 할 수 있다. 즉 Next.js가 이미지 로딩의 우선순위를 둘 수 있다.

> LCP : Largest Contentful Paint로 페이지의 메인 컨텐츠가 로드되었을 가능성이 있을 때 페이지 로드 타임라인에 해당 시점을 표시하여 사용자가 감지하는 로드 속도를 측정할 수 있는 메트릭스 -> LCP가 빠르다 = 해당 페이지를 사용할 수 있다

LCP element는 보통 viewport 에서 가장 큰 이미지거나 텍스트로 본다. `next dev` 실행시에, `<Image>` 에 `priority`가 설정되어 있지 않다면, Next는 자체적으로 경고창을 띄운다. 예시는 다음과 같다.

```javascript
import Image from "next/image";

export default function Home() {
  return (
    <>
      <h1>My Homepage</h1>
      <Image
        src="/me.png"
        alt="Picture of the author"
        width={500}
        height={500}
        priority
      />
      <p>Welcome to my homepage!</p>
    </>
  );
}
```

## Image Sizing

이미지가 성능에 영향을 미치는 경우는 보통 `layout shift`가 일어났을 때 이다. 이러한 `layout shift`는 이미지가 로딩되면서 다른 요소들을 밀치고 삽입되는 경우이다. 이런 문제는 Client 입장에서 매우 불편하고 `CLS`를 일으킨다. 이러한 문제를 해결하는법은 언제나 이미지의 사이즈를 조정하는것이다. 그럼으로써 브라우저딴에서 항상 로딩되기 전에 이미지의 자리를 예약 할 수 있다.

`next/image`가 항상 좋은 성능을 위해 디자인 되었으므로, `layout shift` 현상은 일어나서는 안된다. 즉 아래 세가지 규칙중 하나를 사용해야 된다.

1. static import를 사용

2. `width` 와 `height` 요소를 사용

3. `layout="fill"`을 사용

3번 같은 경우 내 이미지의 사이즈를 모를 때 사용한다. parent element 사이즈만큼 image가 사이즈될 수 있게 설정하게 된다. 그럼 `objectFit property` 를 `fill`, `contain`, `cover`, `objectPosition property`와 함께 사용하여 얼마나 이미지가 차지해야되는지 알 수 있다.

만약 내가 아는 소스에서 이미지를 가져오는 것이라면, image의 pipeline을 일반화 하는것이 좋다.

만약 API call 을 사용하여 이미지를 가져온다면, API call 을 중간에 수정하여 URL과 함께 이미지의 크기를 리턴하는것이 가능하다.

만약 위 3가지의 방법을 사용하지 않는다면 `next/image`는 `<img>`와 차이가 없다.

## Styling

Styling 같은 경우에 `<img>`와 크게 다를것 없지만, 주의사항이 몇가지 존재한다.

1. 올바른 layout mode 설정하기

2. 이미지를 className으로 지정하기 (DOM 구조에 맞추지 말것)

보통 layout에서 `<img>` 는 `<span>`에 둘러싸여 있다. 이러한 추가적인 `<span>`은 layout shift를 만드는 주된 원인이다. 즉 `<img>`에 `className`을 붙여 CSS Module을 사용하는것이 일반적이다. 혹은, `className`을 사용하여 `global stylesheet`를 사용할 수 도 있다.

3. `layout='fill'`을 한다면 `position:relative` 도 함께 사용해야된다

4. `layout='responsive'`를 사용할 시 `display:block`을 같이 사용할 것

## Font Optimization

버전 `10.2` 부터 Next.js 는 built-in web font optimization 을 지원하게 되었다.

일반적으로 Next.js는 자동으로 CSS를 빌드타임에 넣어, 추가적인 반복작업을 피한다. 이렇게 함으로써 FCP 와 LCP에 도움이 된다.

| `FCP(First Contentful Paint)`란 페이지가 로드되기 시작한 시점부터 컨텐츠의 일부가 화면에 렌더링 될 때까지의 시간 `LCP(Largest Contentful Paint)`란 페이지가 로드되기 시작한 시점부터 뷰포트 내부의 가장 큰 이미지 또는 텍스트의 렌더링까지 걸리는 시간

```javascript
// Before
<link
  href="https://fonts.googleapis.com/css2?family=Inter&display=optional"
  rel="stylesheet"
/>

// After
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<style data-href="https://fonts.googleapis.com/css2?family=Inter&display=optional">
  @font-face{font-family:'Inter';font-style:normal...
</style>
```

### Usage

Webfont 를 Next.js에 넣으려면 `Document`안에 폰트를 삽입하면 된다.

```javascript
// pages/_document.js

import Document, { Html, Head, Main, NextScript } from "next/document";

class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          <link
            href="https://fonts.googleapis.com/css2?family=Inter&display=optional"
            rel="stylesheet"
          />
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default MyDocument;
```

특이한점은 해당 폰트는 특정한 페이지에만 사용되므로 `next/head`를 사용하지 않는다는 점이다. 현재 Automatic Webfont Optimization 은 Google Fonts에 의해 지원된다.

### Disabling Optimization

Font Optimization 을 끄는 방식은 아래와 같다.

```javascript
// next.config.js

module.exports = {
  optimizeFonts: false,
};
```
