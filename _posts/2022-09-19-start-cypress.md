---
layout: post
title: Cypress로 e2e 테스트 하기
subtitle: Cypress 머리부터 발끝까지 학습하기
categories: React
tags: [React, Testing]
---

## Cypress로 진행하는 e2e 테스트

> 환경: React, typescript, styled-components

## 테스트를 해야되는 이유?

---

사실 테스트를 진행해야 되는이유는 정말 셀 수 없이 많다.

- 개발 과정에서 문제가 생겼을 때 바로 눈치 챌 수 있디
- 리팩토링을 믿고 할 수 있다
- 바로 코드의 동작 상태를 확인할 수 있다
- 테스트 코드를 만들기 위해 코드를 하다보면 컴포넌트 분할 및 클린코드가 자연스럽게 이루어진다
- 품질, 안전, 성능을 확인할 수 있다
- 지속적인 운영을 보장한다
- 위험을 완화할 수 있다
- 등등..

하지만 이러한 이유들을 포함하여 도입한 이유 중 큰 이유는 우리 프로젝트가 `Agile` 방법론을 따르고 있기 때문이다.

> “애자일의 기술 실천 방법은 모든 애자일 활동 중 가장 핵심적인 요소다.
> 기술 실천 방법 없이 애자일을 도입하려는 시도는 실패할 수밖에 없다.”

그리고 이러한 애자일의 실천방법 중 대표적인 방법이 바로 `TDD` Test Driven Development 이다. 하지만 아직 Test를 먼저 짜고 코드를 그에 맞춰 개발하는 레벨에는 도달하지 못하여 우선 e2e 테스트 코드를 짜는 연습부터 시작하기로 했다.

---

## Cypress 설치하기

### dependency 설치

우선 cypress dependency 를 추가해준다

```shell
yarn add --save-dev cypress
```

#### Styled Component 처리

```shell
yarn add --save-dev react-remove-properties
```

테스트를 위해서 component에 `[data-cy=”new-component”]` 이런식으로 선언해주는데 빌드 과정에서 없애주는 dependency를 추가로 설치해준다. 그리고 babelrc에서 빌드하기 전에 처리하지 않는 작업도 포함해주자.

```json
"env": {
  "production": {
    "plugins": [
      [
        "react-remove-properties",
        {
          "properties": ["data-cy"]
        }
      ]
    ]
  }
}
```

### config 설정

그 다음 cypress가 원하는대로 작동할 수 있게 config설정을 마무리 해준다.

```typescript
// cypress.config.ts
import { defineConfig } from "cypress";

module.exports = defineConfig({
  e2e: {
    setupNodeEvents(on, config) {
      // implement node event listeners here
    },
    baseUrl: "http://localhost:3000",
  },
  projectId: "ownProjectId",
});
```

baseUrl같은 경우에는, 테스트할 서버의 주소가 되며, projectId는 추후에 cypress 앱과 연동하여 record 확인등을 할 시에 인증 용도로 사용된다.

그리고 cypress는 테스트 코드를 실행하다보면, 자연스럽게 스크린샷과 영상을 찍어서 로컬에 저장해두는데, 깃이 무거워지기 전에 ignore도 미리 해두자

```gitignore
# ...

cypress/videos
cypress/screenshots
```

### 폴더 구조

내가 사용하고 있는 Cypress 의 폴더 구조는 다음과 같다.

```
+--cypress
      +--e2e
      |   +--1-main-page
      |   |      +--login.cy.ts
      |   |      +--main.cy.ts
      |   |      +--register.cy.ts
      |   +--2-ready-page
      |          +--ready-modal.cy.ts
      |          +--ready.cy.ys
      +--fixtures
      |   +--allRooms.json
      |   +--loginResult.json
      |   +--profile.json
      +--support

```

e2e 폴더에 실제로 테스트할 코드들을 페이지별로 분리했고,
fixtures 폴더에는 추후에 사용할 모킹하였을 때 결과로 받을 json 형식들을 미리 만들어 두었다.

---

## 테스트 코드 작성하기

간단하게 main page를 e2e 테스를 진행해보자. 테스트해볼 사안들은 다음과 같다.

- 타이틀이 렌더이 잘 되는지
- Header가 잘 렌더링 되었는지
  - 로고
  - 로그인 버튼
- Footer가 잘 렌더링 되었는지
  - Terms, Privacy Policy 버튼
  - 정보
- 랜딩 페이지가 잘 렌더링 되었는지
- 방 정보API를 잘 호출하는지
- 방 정보가 잘 렌더링 되었는지
  - 방장 정보
  - 방의 제목
  - 방의 설명

그럼 천천히 진행해보도록 하자.

### describe

이 부분은 거의 모든 테스트코드 라이브러리들은 다 유사한 것 같다. describe 에서 내가 어떠한 컴포넌트 혹은 페이지를 검사할지 대주제를 정하고 it에서 개개인의 테스트들을 실제로 구현한다.

#### beforeEach

매번 it를 실행하기 전, 어떠한 역할을 수행할지 미리 정할 수 있다. 예를들어 모킹이 필요한 경우, 여기서 어떤 요청을 intercept해서 가져올지, it를 실행하기 전에, 어떤 페이지를 방문할지, viewport는 어떤 비율로 볼지 등 설정 할 수 있다.

#### intercept

여기서 가장 중요한 부분이다. Cypress는 e2e 테스트이기에 모든 방면에서 다 테스트를 진행하기에 BE에 API 요청은 당연히 일어난다. 하지만, 실제 BE에 API를 요청한다면 그건 완벽한 테스트가 될 수 없다.

> CREATE 같은 경우 매번 BE에 요청하기에도 애매...

그래서 요청을 보내기전에 method와 url 기준으로 intercept해서 어떠한 요청을 주거나 받을지 미리 설정할 수 있는 방법이다.

```typescript
cy.intercept(
  { method: "GET", url: `/api/v1/rooms` },
  { fixture: "allRooms.json" }
);
```

해석을 간단하게 해보자면, `GET`요청을 `/api/v1/rooms`에 요청하게 되면, 실제로 `GET`을 하지는 말고, fixtures 디렉토리에 저장해둔 `allRooms.json`을 가져오라는 뜻이다.

그렇게 완성된 beforeEach는 다음과 같다.

```typescript
beforeEach(() => {
  cy.intercept({ method: "GET", url: API.ROOM }, { fixture: "allRooms.json" });
  cy.visit("http://localhost:3000");
  cy.viewport(1536, 960);
});
```

### it

> 이제 실제로 테스트케이스들을 작성할 준비가 되었다!

우선 cypress의 문법을 알아야된다. 정말 방대하지만, cypress.io에 정리가 잘 되어 있는지 천천히 읽어보면 좋을 것 같다.

> [docs.cypress.io](https://docs.cypress.io/api/table-of-contents)로 바로가기

간단한 문법들은 다음과 같다.

1. `cy.get('')` 으로 원하는 컴포넌트를 선택 할 수 있다. 빈칸에는 class 혹은 id 등으로 검색이 가능하지만, cypress 의 best practice 는 data-cy 태그를 이용하여 unique한 컴포넌트를 가져오는 것이라 하며, 컴포넌트에 추가하여 손쉽게 적용시킬 수 있다.
2. `.should()` 명령어로 방금 get()에서 가져온 컴포넌트가 어떠한 동작들을 해야하는지 선언 할 수 있다. 예를들어 `should('be.visible')` 이라면 선택된 컴포넌트가 보여야된다는 것이며, `should('have.text','word')` 라면, `word`라는 단어를 꼭 포함해야 된다는 뜻이다. 만약 찾는것이 실행되고 있는 비디오라면, 이런것도 가능하다.
   ```typescript
   cy.get('[data-cy="custom-video-screen"]').should(
     "have.prop",
     "paused",
     false
   );
   ```
3. `cy.wait()` 를 이용하여 ms단위로 cypress 의 테스트를 멈춰 둘 수 있다. 영상으로 볼 때 워낙 빠르게 지나가기에, 한번씩 테스트 후에 `wait()`를 두어 눈으로 따라가는것도 괜찮았다.
4. `cy.and()` 앞선 명령어를 연계해서 사용할 수 있게 도와준다. 예로 이런것도 가능하다.
   ```typescript
   cy.get('[data-cy="dropbox-item"]')
     .should("have.length", 1)
     .and("contain.text", "fake_device_0")
     .click();
   ```

> 간단한 예시코드는 다음과 같이 만들 수 있다!

```typescript
it("render Main Props", () => {
  // check if title is visible
  cy.title().should("include", "MODOCO");

  // check if login button is visible and clickable
  cy.get('[data-cy="main-login-button"]')
    .should("be.visible")
    .should("have.text", "로그인");

  // check modoco logo
  cy.get('[data-cy="modoco-logo"]').should("contain", "modoco");

  // check if all footer props are visible
  cy.get('[data-cy="main-footer"]').should("be.visible");
  cy.get("button")
    .should("contain", "Terms")
    .should("contain", "Privacy Policy");
});
```

완성된 테스트코드를 Cypress로 실행하면 다음과 같은 결과를 얻을 수 있다.

> 메인 페이지 테스트
> ![ezgif-4-1251ac46b2](https://user-images.githubusercontent.com/66371206/191083487-d63d6966-6867-47ca-abcd-b69d9e23e205.gif)

> 레디 페이지 테스트
> ![ezgif-4-350cc82de1](https://user-images.githubusercontent.com/66371206/191083493-8de8438b-ff4d-4f69-a947-35ddafd58276.gif)

---

참고문서

[https://www.kiwa.com/kr/ko/services/testing/why-should-i-have-my-product-tested-or-evaluated/](https://www.kiwa.com/kr/ko/services/testing/why-should-i-have-my-product-tested-or-evaluated/)
[https://tech.kakao.com/2021/11/08/test-code/](https://tech.kakao.com/2021/11/08/test-code/)
