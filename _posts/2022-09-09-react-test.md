---
layout: post
title: 리액트 테스팅하기
subtitle: Jest, React-Testing-Library로 리액트 테스트하기
categories: react
tags: [javascript, react]
---

## Jest와 React-Testing-Library를 사용하여 테스트를 해보자

이번에는 모킹 없이 간단하게 DOM관련 테스트만 진행할 예정이다. 모킹 관련은 다음 포스트에서 다룰 예정.

여러 테스트 예시들을 보면, 한 `jest.it` 함수 에서 render를 여러번 하는것을 손쉽게 볼 수 있다. 그렇게 나도 유사하게 해보며 익혀보았다.

## 간단한 사용법 익히기

### describe

describe에서 내가 어떠한 component를 할지 먼저 알려준다.

```typescript
describe("<MyPageForm />", () => {});
```

### it

it에서 어떠한 동작들을 테스트할 지 선언하여 알려줄 수 있다.

```typescript
it("matches snapshot", () => {});
```

### render

그리고 어떤 컴포넌트를 render할지 it안에 선언을 해준다. 해당 render는 react testing library의 기능이다.

```typescript
const utils = render(
  <Router>
    <MyPageForm />,
  </Router>
);
```

> ESLint에서 useNavigate를 사용하는 component인 경우 BrowseNavigate 컴포넌트 안에 선언해야 하기에 BrowserRouter as Router로 감싸주었다

### expect

실제로 방금 선언한 render인 `utils`에 어떠한 동작들이 수행되어야 하는지 정의 할 수 있다.

이번 `it`는 단순히 이러한 `utils.container` 가 snapshot에 매칭 되는지 확인하는 요소이기에 간단하게 `toMatchSnapshot`함수를 사용하여 확인할 수 있다.

```typescript
expect(utils.container).toMatchSnapshot();
```

최종적으로 구현된 간단한 형태는 다음과 같다.

```typescript
describe("<MyPageForm />", () => {
  it("matches snapshot", () => {
    const utils = render(
      <Router>
        <MyPageForm />,
      </Router>
    );
    expect(utils.container).toMatchSnapshot();
  });
});
```

그리고 다음 it에서 원하는 텍스트가 존재한느지 있는지 확인하려면 다음과 같이 추가할 수 있다.

```typescript
describe("<MyPageForm />", () => {
  it("matches snapshot", () => {
    const utils = render(
      <Router>
        <MyPageForm />,
      </Router>
    );
    expect(utils.container).toMatchSnapshot();
  });
  it("has a texts", () => {
    const utils = render(
      <Router>
        <MyPageForm />,
      </Router>
    );
    // MyPage에 있는지 확인
    utils.getByText("아바타 재생성");
    utils.getByText("프로필 이미지");
    utils.getByText("프로필 수정");
    utils.getByText("수정 완료");
  });
});
```

이런 테스트 코드는 동작하는데에는 문제가 없어 보이지만, 실제로 테스트 코드가 길어지거나, 다양한 동작을 할 때에 문제가 생길 수 있다. 즉 리액트에서 컴포넌트 분할을 하듯이, 테스트 코드 또한 컴포넌트로 쪼개고 재사용할 수 있어야 된다.

> 여기서도 utils를 재사용하는것을 보아 그렇게 좋지않음을 알 수 있다.

## 컴포넌트 단위로 쪼개기

조금 더 복잡한 SignUp 페이지를 가지고 테스트를 해보겠다.

### 기능 빼내기

우선 재사용할 수 있는 기능들을 `describe`가 아닌 `function`에 빼두자.

```typescript
function renderSignUp() {
  // mocking submit button -> not used
  const onSubmit = jest.fn();

  // render SignUpForm
  const result = render(
    <Router>
      <SignUpForm />
    </Router>
  );

  // get blocks
  const Heading = () => result.getByText("회원가입", { selector: "h1" });
  const Nickname = () => result.getByPlaceholderText("닉네임을 입력해주세요.");
  const Email = () => result.getByPlaceholderText("이메일을 입력해주세요.");
  const FirstPassword = () =>
    result.getByPlaceholderText(
      "8~16자 영문 대 소문자, 숫자, 특수문자를 사용하세요."
    );
  const SecondPassword = () =>
    result.getByPlaceholderText("비밀번호를 다시 입력해주세요.");

  // make events for blocks
  const typeNickname = (name: string) => {
    userEvent.type(Nickname(), name);
  };
  const typeEmail = (name: string) => {
    userEvent.type(Email(), name);
  };
  const typeFirstPassword = (pass: string) => {
    userEvent.type(FirstPassword(), pass);
  };
  const typeSecondPassword = (pass: string) => {
    userEvent.type(SecondPassword(), pass);
  };
  const SignUpButton = () =>
    result.getByText("회원가입", { selector: "button" });
  const clickSubmit = () => {
    userEvent.click(SignUpButton());
  };

  return {
    result,
    Heading,
    Nickname,
    Email,
    FirstPassword,
    SecondPassword,
    typeNickname,
    typeEmail,
    typeFirstPassword,
    typeSecondPassword,
    SignUpButton,
    clickSubmit,
    onSubmit,
  };
}
```

이런식으로 테스트할 함수들을 분리하여, 추후에 손쉽게 필요한 함수들을 선언하여 관리도 용이하며, 재사용도 쉽게 설정할 수 있다.

userEvent의 옵션으로는 다음과 같이 정리되어 있다.

```typescript
declare const userEvent: {
  click: typeof click;
  dblClick: typeof dblClick;
  type: typeof type;
  clear: typeof clear;
  tab: typeof tab;
  hover: typeof hover;
  unhover: typeof unhover;
  upload: typeof upload;
  selectOptions: (
    args_0: Element,
    args_1: string | string[] | HTMLElement | HTMLElement[],
    args_2?: MouseEventInit | undefined,
    args_3?: import("./utils").PointerOptions | undefined
  ) => void;
  deselectOptions: (
    args_0: Element,
    args_1: string | string[] | HTMLElement | HTMLElement[],
    args_2?: MouseEventInit | undefined,
    args_3?: import("./utils").PointerOptions | undefined
  ) => void;
  paste: typeof paste;
  keyboard: typeof keyboard;
};
```

render같은 경우 너무나 방대하여, 그때 그때 필요한 쿼리를 선택하여 찾을 수 있다. 대충 보아하니, query, parameter, role, text 등 정말 원하는 정보를 다 찾을 수 있을 것 같다.

### 렌더링 확인

아까 위에서 했던것 처럼 원하는 버튼 혹은 컴포넌트가 존재하는지 조금 더 직관적으로 위에서 선언한 함수들을 가지고 구현할 수 있다.

```typescript
it("sign up form rendering", () => {
  const {
    Heading,
    Nickname,
    Email,
    FirstPassword,
    SecondPassword,
    SignUpButton,
  } = renderSignUp();

  // check if blocks are rendered
  expect(Email()).toBeInTheDocument();
  expect(Nickname()).toBeInTheDocument();
  expect(Heading()).toBeInTheDocument();
  expect(FirstPassword()).toBeInTheDocument();
  expect(SecondPassword()).toBeInTheDocument();
  expect(SignUpButton()).toBeInTheDocument();
});
```

이렇게 함으로써, 짧고 보다 확실하게 어떠한 요소들이 있어야 되는지 확인할 수 있는 테스트 코드가 완성되었다.

### 동작확인

아무래도 이번에 테스트 하는 컴포넌트가 SignUp이다 보니, 동적인 동작들이 이루어져야한다. 예를들어, 내가 Nickname을 친다던가, password를 타이핑 하는 요소들이 들어갈 수 있다. 앞서 이용한 `type*(string)` 함수를 이용하여, 간단하게 구현이 가능하다.

```typescript
it("form 값으로 onSubmit을 호출.", async () => {
  const {
    Email,
    Nickname,
    FirstPassword,
    SecondPassword,
    typeNickname,
    typeEmail,
    typeFirstPassword,
    typeSecondPassword,
    clickSubmit,
  } = renderSignUp();

  // make events for blocks
  typeNickname("영기");
  typeEmail("yeonggi@mail.com");
  typeFirstPassword("somatest0909!");
  typeSecondPassword("somatest0909!");
  clickSubmit();

  // check if events are working
  expect(Email()).toContainHTML("yeonggi@mail.com");
  expect(Nickname()).toContainHTML("영기");
  expect(FirstPassword()).toContainHTML("somatest0909!");
  expect(SecondPassword()).toContainHTML("somatest0909!");
});
```

여기에서 `clickSubmit()`함수를 앞서 `jest.fn()`라는 콜백 함수로 불러왔었는데, 이 부분은 추후에 모킹을 하면서 제대로 이용할 예정이다. 모킹할 곳을 만들어 두고, submit을 하였을때 어떠한 json 타입이 오는지 `expect`해서 찾아 낼 예정이다.

무사히 테스트 코드를 작성 후 돌려보면, 이렇게 테스트 결과가 통과했다고 나오게 된다.
<img width="622" alt="스크린샷 2022-09-13 오후 2 49 55" src="https://user-images.githubusercontent.com/66371206/189819890-a8ea5e99-693a-4801-ac22-c01e233637f1.png">

참고 : [확장가능한 테스트](https://javascript.plainenglish.io/declarative-and-scalable-testing-with-react-testing-library-177f35f41396)
