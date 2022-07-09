---
layout: post
title: Typescript React Styled-Component 에서 Font 적용하기
subtitle: CRA 환경에서 Font 적용하기
categories: TIL
tags: [TIL, TypeScript]
---

## Typescript, React, Styled-Component 환경에서 Font 적용하기

프로젝트를 진행하다보니, Font를 설정해야 되는데, 일반적으로 App.css를 사용하는 방식이 아니라, Styled-Component를 사용했기에 조금 더 복잡했다.

## 적용순서

### Font 다운받기

font는 OpenSource 나 원하는곳에서 잘 다운 받으면 된다. 나는 [눈누](https://noonnu.cc)라는 폰트 저장소를 애용한다. 저작권에 주의하며 다운받자.

다운을 받을 때, ttf, otf, woff 중 어떤것을 받을지 선택 할 수 있는데, 우선 ttf로 적용하였다.

`ttf`는 true type font `otf`는 open type font 으로 pc에 설치하는 폰트이며 `woff`는 web open font format 으로 웹에서 사용하는 포맷이다.

> 로딩속도는 woff2 > woff > ttf 순서

그럼 잘 다운받은 폰트를 src폴더 안에 fonts라는 디렉토리를 만들어 잘 저장해둔다.

프로젝트 내부의 폴더구조는 다음과 같다
<img width="273" alt="image" src="https://user-images.githubusercontent.com/66371206/178093254-5ca4bbff-b528-41a3-a7c0-d17afe8a2d9d.png">

### GlobalStyle에 Font 추가

원래는 App.css에 font를 적용시켜 `<App>`에 감싸주기만 하면 됐다. 하지만, 지금은 Styled-Component를 사용하므로 GlobalStyle로 잘 적용시켜보자.

우선 styles 디렉토리에 있는 GlobalStyle을 담당하고있는 global.ts에 사용하고싶은 font들을 추가해주었다.

```typescript
// global.ts
import { createGlobalStyle } from 'styled-components';
import GmarketSansTTFBold from '../fonts/GmarketSansTTFBold.ttf';
import GmarketSansTTFMedium from '../fonts/GmarketSansTTFMedium.ttf';
import GmarketSansTTFLight from '../fonts/GmarketSansTTFLight.ttf';

const GlobalStyle = createGlobalStyle`
  @font-face {
        font-family: 'GmarketSansTTFBold';
        src: local('GmarketSansTTFBold'), local('GmarketSansTTFBold');
        font-style: normal;
        src: url(${GmarketSansTTFBold}) format('truetype');
  }
  @font-face {
        font-family: 'GmarketSansTTFMedium';
        src: local('GmarketSansTTFMedium'), local('GmarketSansTTFMedium');
        font-style: normal;
        src: url(${GmarketSansTTFMedium}) format('truetype');
  }
  @font-face {
        font-family: 'GmarketSansTTFLight';
        src: local('GmarketSansTTFLight'), local('GmarketSansTTFLight');
        font-style: normal;
        src: url(${GmarketSansTTFLight}) format('truetype');
  }
```

여기서 문제가 하나 생기는데, `.ttf` 파일을 제대로 import 해줄 수 없다. 해당 문제는 src의 디렉토리위에 `fonts.d.ts`를 만들어 해당 타입의 모듈을 import 해줄 수 있게 수정해주면 된다.

```typescript
// fonts.d.ts
declare module "*.ttf";
```

### GlobalStyle 적용

이제 index.tsx에 폰트들이 추가된 GlobalStyle을 잘 둘러주기만 하면 된다.

```typescript
import App from "./App";
import GlobalStyle from "./styles/global";

const rootElement = document.getElementById("root");
if (!rootElement) throw new Error("Failed to find the root element");
const root = createRoot(rootElement);
root.render(
  <StrictMode>
    <GlobalStyle />
    <App />
  </StrictMode>
);
```

### 사용하고 싶은 Font를 Component에 적용

이제 해당 App 안에 있는 컴포넌트에서 자유롭게 폰트를 꺼내서 사용할 수 있다.

```typescript
// main.tsx
...
const Title = styled.p`
  margin-top: 5rem;
  height: 10rem;
  font-size: 10rem;
  font-family: GmarketSansTTFLight, sans-serif, Arial;
`;
```

<img width="651" alt="image" src="https://user-images.githubusercontent.com/66371206/178093400-3c5fa099-f561-4f21-954e-650dfa98b0b5.png">

잘 적용된 모습을 볼 수 있다.

## 후기

이번에는 우선 ttf로 통일을 했지만, 역시나 개선을 위해 woff로 하는것이 좋아보인다. 튜닝을 하게된다면, 웹폰트로 지정을 해서 로딩속도를 개선해보자.
