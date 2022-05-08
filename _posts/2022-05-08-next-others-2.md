---
layout: post
title: NEXT-Others-2
subtitle: NEXT의 Environment Variables, Browsers, Features, Scripts에 대하여 알아보자
categories: next.js
tags: [next.js, javascript, web, study]
---

## Environment Variables

### Loading Environment Variables

`Next.js`는 우선 내부적으로 `.env.local` 과 `process.env` 같은 환경변수를 제공해준다. 예를들어 `.env.local` 같은 경우에는 다음과 같이 사용한다.

```shell
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

해당 환경변수들은 Node.js에서 자동으로 `Next.js`의 데이터를 가져오는 방식으로 사용하게 된다. 예를들어 해당 환경변수를 가지고 ` getStaticProps`를 사용하는 방식을 보면 다음과 같다.

```javascript
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  });
}
```

| 서버가 민감한 정보들을 안전하게 다루기 위해 Next.js는 `process.env.*` 파일을 빌드 시간에 맞는 경로로 전달한다. 즉 `process.env`는 자바스크립트에서의 표준이 아니며, object destructuring을 사용할 수 없다는 뜻이다. 즉 환경 변수는 `const { PUBLISHABLE_KEY } = process.env` 처럼 사용하는 것이 아닌 `process.env.PUBLISHABLE_KEY` 처럼 사용해야 된다.

또한 Next.js는 (`$VAR`) 과 같은 변수들은 자동적으로 `.env*` 파일에서 연장하여 사용한다. 변수를 사용한 방법은 다음과 같다.

```shell
# .env
HOSTNAME=localhost
PORT=8080
HOST=http://$HOSTNAME:$PORT
```

만약 `$` 를 그대로 쓰고싶다면 `\$` 과 같이 백슬래쉬를 넣어 사용해야된다.

### Exposing Environment Variables to the Browser

일반적인 환경변수들은 `Node.js` 환경에서만 사용가능하므로, 브라우저에 유출이 되진 않는다. 만약 브라우저에서 `NEXT_PUBLIC` 으로 시작하는 변수를 외부로 유출시키고 싶다면, 다음과 같이 사용하면 된다.

```shell
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

이렇게 사용하게 되면, `process.env.NEXT_PUBLIC_ANALYTICS_ID`가 로드되어, Node.js 환경으로 자동으로 들어가 코드 어디에서도 사용 할 수 있게 된다. 즉 `NEXT_PUBLIC_`과 같은 접두사를 사용하게 된다면, 자바스크립트가 브라우저에게 바로 값을 넘겨주게 된다. 이러한 인라인은 빌드시간에 일어나며, 다음과 같은 `NEXT_PUBLIC_`의 환경변수들은 프로젝트가 빌드가 끝난 후 사용된다. 코드에서 사용되는 예시는 다음과 같다.

```javascript
// pages/index.js
import setupAnalyticsService from "../lib/my-analytics-service";

// NEXT_PUBLIC_ANALYTICS_ID can be used here as it's prefixed by NEXT_PUBLIC_
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID);

function HomePage() {
  return <h1>Hello World</h1>;
}

export default HomePage;
```

### Default Envrionment Variables

일반적으로는 하나의 `.env.local`파일만이 필요하다. 하지만 가끔 `development`나 `production`에 따라 다른 환경 변수들을 사용 할 수 있다. 그래서 `Next.js` 는 `.env`, `.env.development`, `.env.production`과 같은 환경변수 또한 지원하며 `.env.local`을 항상 표준으로 둔다.

| 주의사항으로는 `.env`, `.env.development`, `.env.production` 과 같은 파일들은 `.env*.local`에 정의되어 있어야 하고 `.gitignore`에 포함시켜 안전하게 보관해야된다.

### Test Envrionment Variables

`development`와 `production` 환경변수외로, `text`라는 세번째 옵션이 또 존재한다. 앞선 방법들과 마찬가지로 `.env.teest`에서 `testing` 환경을 구축하여 사용 할 수 있다.

이러한 방법은 `jest`나 `cypress`와 같은 테스팅 툴을 사용할 때 유용하게 사용 할 수 있다. 이러한 테스트 방식은 `NODE_ENV`가 `test`로 되어있다면, 테스트의 표준 환경을 사용 할 수 있다.

`test` 가 `development` 와 `production` 와 다른점이 있다면, 테스트 환경은 `.env.local`에 로딩이 안된다는 것이다. 이러한 방식으로 실행되어야 매번 같은 환경을 사용할 수 있기 때문이다.

unit test 를 하는 동안에는 `@next/env` 패키지를 사용하여 `loadEnvConfig`를 해야함에 주의한다.

```javascript
// The below can be used in a Jest global setup file or similar for your testing set-up
import { loadEnvConfig } from "@next/env";

export default async () => {
  const projectDir = process.cwd();
  loadEnvConfig(projectDir);
};
```

env의 우선순위는 다음과 같다.

#### NODE_ENV=production

1. .env.production.local
2. .env.local
3. .env.production
4. .env

#### NODE_ENV=development

1. .env.development.local
2. .env.local
3. .env.development
4. .env

#### NODE_ENV=test

1. .env.test.local
2. .env.test
3. .env

## Supported Browsers and Features

## Script Component

Image를 NEXT 에서 제공했던것 처럼, Script같은 경우에도 따로 지정해주는걸 사용하는것이 효율과 성능면에서 더 좋다. 해당 Script 컴포넌트는 `next/script`에서 가져올 수 있으며 기본적인 HTML `<script>`의 연장선이다. 해당 <Script />는 `next/head` 밖에서 개발자들에게 로딩하는 우선순위를 지정할 수 있게 해주어 로딩 효율을 높여준다.

```javascript
import Script from "next/script";

export default function Home() {
  return (
    <>
      <Script src="https://www.google-analytics.com/analytics.js" />
    </>
  );
}
```

### Overview

웹사이트는 가끔 통계, 광고, 위젯등을 사용할 때 써드파티의 스크립트를 사이트에서 사용하게 된다. 하지만 이러한 스크립트를 가져오는것이 보통 유저와 개발 입장에서 둘 다 문제를 일으키게 된다.

- 어떠한 써드파티의 스크립트 같은경우 너무 무거워서 로딩효율이 안좋고, UX 관점에서 되게 안좋을 수 있다. 특히나, render-block 혹은 딜레이가 생길 수 있다.
- 개발자들은 어떠한 써드파티의 스크립트를 어디에 두어야 효율적인 로딩이 될지 고민하게 된다.

이러한 문제들을 Next 에서 제공하는 `Script`를 사용하여 해결 할 수 있다.

### Usage

이러한 `Script`를 사용시에, `strategy`란 속성을 두어서 관리를 할 수 있다. 간단한 예제를 보면 다음과 같다.

```javascript
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />
```

속성들을 보면 다음과 같다.

- [beforeInteractive](#beforeinteractive)
- [afterInteractive](#afterinteractive)
- [lazyOnload](#lazyonload)
- [worker](#worker)

#### beforeInteractive

`beforeInteractive`속성이 있는 스크립트 같은 경우에는 최초의 HTML에 들어가, 서버가 바로 실행하여 self-bundle된 Javascript가 실행되기 전에 돌아간다. 이러한 방법은 페이지가 상호작용을 하기도전에, fetch하여 실행을 해야될 경우 주로 사용된다. 이러한 방법은 메인 페이지인 `_document.js`에서 동작하며, 보통 모든 사이트에서 로딩이 필요한 script인 경우에 들어가서 실행된다.

이러한 `beforeInteractive`한 방법이 사용되는 이유는, 오직 `_document.js`에서만 동작하게 하여 스티리밍과 Suspense functionality를 지원하기 위함이다. 즉 `_document.js` 의 밖에서는 `beforeInteractive`의 방식이 우선순위를 잘 두어 실행된다는 보장을 할 수 없다.

```javascript
// In _document.js
import { Html, Head, Main, NextScript } from "next/document";
import Script from "next/script";

export default function Document() {
  return (
    <Html>
      <Head />
      <body>
        <Main />
        <NextScript />
        <Script
          src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"
          strategy="beforeInteractive"
        ></Script>
      </body>
    </Html>
  );
}
```

보통 이러한 속성을 띄는 예는 다음과 같다.

- Bot detectors
- Cookie consent managers

#### afterInteractive

`afterInteractive` 방법을 쓰는 Script같은 경우에는 클라이언트 딴에서 삽입되어, Next.js가 hydration 작업을 마친 후에 동작한다. 이러한 방식은 제일빨리 실행될 필요가 없으며 우선순위가 낮을 때 사용하게 된다.

이러한 방식의 스크립트는 다음과 같은 상황에 많이 사용된다.

- Tag managers
- Analytics

#### lazyOnload

`lazyOnload`의 속성을 띄는 Script 같은 경우에는, 모든 리소스들이 로딩된 후 `idle` 한 시간에 fetch되어 정보를 가져올 떄 사용된다. 이러한 방식은 background 나 낮은 우선순위를 가진 스크립트들이 주로 사용된다.

```javascript
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />
```

이러한 방식을 사용하는 스크립트는 주로 다음과 같다.

- Chat support plugins
- Social media widgets

#### worker

해당 속성 같은 경우에는, 메일 쓰레드를 정해주어 효율을 높여주는 방식이지만, 아직 테스트 환경이라 안전하지 않다.
