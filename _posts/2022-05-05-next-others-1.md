---
layout: post
title: NEXT-Others-1
subtitle: NEXT의 Static File Serving, Fast Refresh, ESLint, TypeScript에 대하여 알아보자
categories: next.js
tags: [next.js, javascript, web, study]
---

## Static File Serving

`Next.js`는 파일을 `public`에서 이미지처럼 정적으로 활용 할 수 있다. `public`은 root 안에 있어야 하며, `public` 안에서는 `/`로 시작하는 base URL을 따라가야된다.

`public/me.png`를 삽입하는 과정은 다음과 같다.

```javascript
import Image from "next/image";

function Avatar() {
  return <Image src="/me.png" alt="me" width="64" height="64" />;
}

export default Avatar;
```

`next/img`의 대한 내용은 [NextOptimization](2022-05-04-next-optimization.md) 를 참고.

이러한 폴더는 `robots.txt` 혹은 `favicon.ico` 에서도 사용이 될 수 있으며, Google Site Verification 등에도 활용될 수 있다.

| 주의사항! `public`이란 명명은 다른곳에서 사용하면 안된다. 해당 폴더는 정적인 asset만 포함되어야 된다!

| 주의사항2! 마찬가지로 정적 파일을 `pages/`라는 폴더 안에 넣게되면 에러가 난다!

| 주의사항3! `public` 폴더 안에 들어간 assets 들 만이 빌드 타임에서 Next.js로 넘겨진다. 런타임중에서는 추가가 되어도 활용되지 않으므로, `AWS S3`과 같은 third party를 사용하는 것이 좋다.

## Fast Refresh

Fast Refresh는 React component에 편집을 한 즉시 피드백을 주는 기능이다. 이러한 기능은 Next.jsdml 9.4 이상에서 자동으로 작동하며, 켜져있다면 component 의 상태정보를 잃지 않고 바로 확인을 할 수 있다.

### How It Works

- 만약 `React component`만 export하는 환경이라면, Fast Refresh는 오직 그 한 파일만 업데이트를 하며, 컴포넌트를 다시 렌더링 해준다. 해당 파일 안에서는 style, logic, handler, effects 등 아무거나 편집을 해도 된다!

- `React component`가 아닌 파일을 편집한다면, Fast Refresh는 두 파일 모두와 import 된 다른 파일들을 재실행 할 것이다. 예를들어, `Button.tsx`와 `Modal.tsx`가 `theme.tsx`를 import 한다면, `theme.tsx`를 편집하게 되면, 두 컴포넌트 다 업데이트가 된다.

- 만약 React 구조에서 벗어난 import 파일을 편집하게 된다면, Fast Refresh는 full reload를 어쩔 수 없이 하게된다. 예를들어, 만약 상수 component를 export 하지만, non-React utility file이 import 되어 있다면 파일의 위치를 바꾸어야된다.

### Error Resillience

#### Syntax Errors

만약 개발중에 syntax error가 나온다면, 수정 후 파일을 저장하면 바로 적용이 된다. 오류는 자동으로 없어지고, 앱을 재로딩할 필요도 없다.

| 컴포넌트의 상태정보 또한 잃지 않는다.

#### Runtime Errors

만약 런타임 에러가 나는 코드를 짜버렸다면, contextual overlay가 나온다. 에러를 고치면 바로 overlay가 없어지면서 앱이 재로딩이 된다.

컴포넌트의 상태정보는 렌더링을 하지 않았을 시 저장된다. 만약 렌더링 중에 에러가 나왔다면, 리액트는 업데이트된 코드로 재구동 한다.

만약 앱에 `error boundaries`가 설정 되어있다면, 렌더링 에러 이후 렌더링하려고 바로 재시도를 한다. 즉 `error boundary`가 설정되어 있다면, 루트의 앱 상탲정보를 항상 재설정 해주는 수고를 겪지 않아도 된다. 하지만, `error boundary`가 너무 세분화되면 안된다. 보통 React production에 많이 사용되고, 의도적으로 사용되어야 한다.

### Limitations

Fast Refresh는 컴포넌트를 수정시에도 local React state를 유지하려고 노력한다.

| 안전하다고 판단될떄만 사용해야됨!

local state가 자주 재설정 되는 이유는 다음과 같다.

- Local State는 클래스 컴포넌트에서 예약되어지지 않는다.

- 편집하려는 파일이 다른 export된 리액트 컴포넌트를 사용하고 있을 수 도 있다.

- 파일이 `HOC`같은 상위의 컴포넌트를 export 할 수 있다. 만약 리턴된 형태가 class라면 상태는 리셋된다.

- 익명 화살표함수는 Fast Refresh를 local component state에 저장하지 않는다. 더 큰 코드인 경우에는 `name-default-component`를 사용 할 수 있다.

### Tips

Fast Refresh는 React local state를 함수 컴포넌트로 예약한다

| 만약 강제로 상태를 초기화하고 싶을 수가 있다. `// @refresh reset`과 같이 초기화 할 수 있다.

### Fast Refresh and Hooks

가능하다면 Fast Refresh는 수정중에 컴포넌트에 상태 정보를 저장한다. 특히 `useState` 와 `useRef` 같은 경우에, 인자나 Hook call을 하지 않는 이상 이전값을 저장하고 있다.

`useEffect`, `useMemo`, `useCallback` 같은 의존성이 있는 Hook 같은 경우에는, Fast Refresh중에 항상 update 된다. 이러한 의존성들은 Fast Refresh가 일어나고 있는중에 무시된다. 예를들어, `useMemo(() => x * 2, [x])` 가 `useMemo(() => x * 10, [x])` 로 변환다면, x가 변하지 않았음에도 불구하고 재실행 된다.

위와 같은 방법은 예상치못한 결과를 낳을 수 있다. 예를들어 `uesEffect`가 빈 배열을 줄 수 도 있다. 하지만, 이런 탄력적인 코드를 사용하는것은 좋은 연습이 된다.

## TypeScript

### TypeScript로 npx 설정하는법

새로 설정하는것이라면

```shell
npx create-next-app@latest --ts
# or
yarn create next-app --typescript
# or
pnpm create next-app -- --ts
```

이미 있는 프로젝트에 삽입한다면

```shell
touch tsconfig.json
```

### Static Generation & Server-side Rendering

```typescript
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from "next";

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
};

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
};

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
};
```

### API Routing

```typescript
import type { NextApiRequest, NextApiResponse } from "next";

export default (req: NextApiRequest, res: NextApiResponse) => {
  res.status(200).json({ name: "John Doe" });
};
```

혹은

```typescript
import type { NextApiRequest, NextApiResponse } from "next";

type Data = {
  name: string;
};

export default (req: NextApiRequest, res: NextApiResponse<Data>) => {
  res.status(200).json({ name: "John Doe" });
};
```

와 같이 사용 할 수 있다.

### Custom `App`

```typescript
import type { AppProps } from "next/app";

export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
```

### Ignoring TypeScript Errors

Next.js는 TypeScript 에러가 존재 할 시 build 에서 오류를 낸다.

만약 이러한 오류에도 불구하고 사용을 하고 싶다면 다음과 같이 사용하면 된다.

`next.config.js`에서 `ignoreBuildErrors`를 켜주면 된다.

```javascript
//next.config.js
module.exports = {
  typescript: {
    // !! WARN !!
    // Dangerously allow production builds to successfully complete even if
    // your project has type errors.
    // !! WARN !!
    ignoreBuildErrors: true,
  },
};
```
