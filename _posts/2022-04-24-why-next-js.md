---
layout: post
title: NEXT
subtitle: NEXT.js 를 사용하는 이유
categories: next.js
tags: [next.js, javascript, web, study]
---

## NEXT 란 무엇인가?

`Next`란 간단하게 말하면, SSR을 쉽게 구현하도록 도와주는 프레임워크이다. SSR 이란 Server Side Rendering의 약자로, 쉽게 말하면, Server 딴에서 렌더링 과정을 해준다는 것이다. 그와 반대대는 개념으로는 CSR 즉 Client Side Rendering 이 존재한다.

### SSR vs CSR

React 로 렌더링방식의 차이를 알아보자면, 우선 일반적으로는 `render()` 라는 함수가 실행 된 후 `componentDidMount()` 함수를 실행하여 다시 한번 더 렌더링이 된다. 그에 반에, Next 같은 경우에는 getInitialProps() 라는 함수를 먼저 호출하여 데이터를 렌더링을 우선으로 해준다. 이러한 CSR 은 SPA 의 구동방식이라고도 말한다. SPA는 Single Page 를 서버측에 제공하고, View 에서는 Client 딴에서 라이브러리 혹은 프레임워크를 사용하여 렌더링하는 방식이다. SSR의 장점으로는 초기로딩 속도가 빠르지만, 단점으로는 page 이동시에는 CSR 보다 느리다.

| page 요청마다 중복되는 파일을 내려받아야 되기 때문!

하지만 CSR 같은 경우, 초기로딩 속도가 느린것에 비해 첫 로딩에 모든 파일을 내려받아, 이동시에 필요한 데이터만 불러 사용하여 더 빠르다고 할 수 있다!

### Next 의 작동 원리

하지만 React 는 SPA 적인 페이지를 구현하기 위한 라이브러리이다. 그럼 전통적으로 CSR 인데, 어떻게 Next는 React기반을 SSR로 구현할 수 있을까?

    1. 사용자가 Server에 페이지 접속 요청시 SSR 방식으로 렌더링 된 HTML 을 전송
    2. 브라우저에서 JavaScript 다운로드 후 React 실행
    3. 사용자와 페이지가 상호작용 후 이동시에는 Client딴에서 처리

## Why Next?

### Search Engine Optimization

`Next`를 사용하면 `SEO` 혹은 `검색 엔진 최적화`가 가능하다! 사실 SSR을 이용하는 이유또한 SEO를 하기 위함이라고도 볼 수 있다.

SEO를 함으로써, 브라우저에 노출이 잘 되므로 마케팅 혹은 서비스적인 면에서 무조건 고려해봐야 되는 사항이라 볼 수 있다. 그럼 왜 CSR은 SEO 에서 불리할까? 그건 Search Engine 의 작동원리로 설명 할 수 있다.

### 검색 엔진 작동원리

    1. 검색엔진이 데이터 크롤링 시 JavaScript 파일을 해석 할 수 없어 HTML 파일에서 크롤링한다
    2. CSR 인 경우에는 Client가 받기전에, HTML에는 빈 문서이므로, 데이터를 받아 올 수 없다
    3. SSR 같은 경우, 이미 HTML에 데이터가 포함이 되어있어, 데이터 수집에 용이하다!

즉 초기에 SSR을 함으로써, SEO에 용이하며 브라우저에서 JavaScript를 다운 후 React를 실행하여 다른 페이지로 이동시 CSR방식으로 작동하여 브라우저에서 처리할 때 SPA의 장점 또한 유지 할 수 있다!!

### Code Splitting

`Code Splitting` 또한 Next.js 를 사용하는 주된 이유이다. 그럼 코드 분할이 무엇일까? 우선 기본적인 프로젝트로 생각을 해보자. JavaScript 프로젝트를 생성하면 React 같은 경우 모든 코드를 하나의 Bundle로 받고 실행한다. 규모가 작은 프로젝트라면 문제가 없지만, 만약 프로젝트의 규모가 커질수록, 브라우저가 파싱해야되는 정보가 많아지므로 CPU의 처리속도가 느리거나 메모리가 낮은 기기일 경우 초기 구동속도가 느려질 수 밖에 없다.

이러한 번들링 작업을 Bundle을 동적으로 생성해주는 코드 분할을 제공하면 해결 할 수 있다. 코드 분할을 사용함으로써 Lazy Loading을 해결 할 수 있고, 필요하지 않은 코드들을 로딩하지 않아, 초기 로딩에 필요한 자원또한 줄여 줄 수 있다.

### Hot Module Replacement

HMR 이란 브라우저를 새로고침하지 않고 Webpack으로 빌드한 결과물을 실시간으로 반영시켜주는 설정이다. 즉 새로고침하지 않아도, Module을 Runtime중에 업데이트 시켜준다는 것이다.

즉 Webpack의 엄청나게 느린 build 과정을 더 줄여 줄 수 있다. 하지만 Vite를 쓴다면 그냥 해결이 되지 않을까...?

[참고링크1](https://ivorycode.tistory.com/entry/Nextjs를-사용하는-이유)

[참고링크2](https://medium.com/@msj9121/next-js-제대로-알고-쓰자-8727f76614c9)

[참고링크2](https://dtaxi.tistory.com/1090)
