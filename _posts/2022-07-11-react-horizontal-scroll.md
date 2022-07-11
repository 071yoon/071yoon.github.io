---
layout: post
title: React + Typescript 에서 Horizontal Scroll 구현
subtitle: React Horizontal Scroll
categories: TIL
tags: [TIL, TypeScript]
---

## React 에서 가로 스크롤 구현을 해보자

좌우 스크롤은 직접 구현해도 되지만, 누군가 이쁜 library를 만들어 두었길래, 바로 사용하였다.

```shell
yarn add react-horizontal-scrolling-menu
```

공식 페이지나 깃허브에 상세하게 설명이 있으니, 참고해도 좋아보임

[공식 페이지](https://www.npmjs.com/package/react-horizontal-scrolling-menu)
[깃허브](https://github.com/asmyshlyaev177/react-horizontal-scrolling-menu#readme)

## 사용

### ScrollMenu

ScrollMenu라는 컴포넌트에 Block을 설정해두면, 바로 가로스크롤을 사용할 수 있다.

```typescript

import { ScrollMenu } from 'react-horizontal-scrolling-menu';
import { LeftArrow, RightArrow } from './Arrow';
import Block from './Block';
...

export default function Scrolls() {
  );
  return (
    <Container>
        <ScrollMenu LeftArrow={LeftArrow} RightArrow={RightArrow}>
            {Data.map(
                ({ nickname, avatar, itemId }: Interface) => {
                    return (
                        <Block
                            itemId={itemId}
                            nickname={nickname}
                            avatar={avatar}
                        />
                    );
                },
            )}
        </ScrollMenu>
    </Container>
  );
}
const Container = styled.div`
  overflow: hidden;
  .react-horizontal-scrolling-menu--scroll-container::-webkit-scrollbar {
    display: none;
  }
  .react-horizontal-scrolling-menu--scroll-container {
    -ms-overflow-style: none; /* IE and Edge */
    scrollbar-width: none; /* Firefox */
  }
`;
```

일반적으로 이렇게 구현을 하면, horizontal-scrolling-scroll-bar가 생기게 된다. 하지만, 일반적으로 가로 스크롤바를 사용하는 경우에 안그래도 비좁은데 스크롤바 까지 생겨버리면 너무 못생겨지기 때문에 삭제 코드를 넣어둔다. 그게 위 Container에서 선언한 style 이다.

```css
.react-horizontal-scrolling-menu--scroll-container::-webkit-scrollbar {
  display: none;
}
.react-horizontal-scrolling-menu--scroll-container {
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none; /* Firefox */
}
```

이렇게만 구현을 해도, 일반적으로는 문제가 없다. 하지만, 마우스를 쓰는 유저에게 문제가 생기는데, 마우스는 버티컬 움직임을 제어 할 수 없어 우측방향 스크롤이 불가능하게 된다. 심지어, 위에서 못생겼다고 스크롤바까지 없애버린 상황! 그걸 해결하기위해 우측 좌측으로 하나씩 화살표를 넣어 버튼형식으로 이동할 수 있게 설정해 두었다.

### Arrow

Block은 그냥 일반적으로 디자인을 해도 상관이 없으니 넘어가고, Arrow 부분을 살펴보겠다.

```typescript
import React, { useState } from "react";
import styled from "styled-components";
import { VisibilityContext } from "react-horizontal-scrolling-menu";

function Left({
  children,
  onClick,
}: {
  children: React.ReactNode;
  onClick: () => void;
}) {
  const [show, setShow] = useState(false);
  return show ? (
    <Button
      onClick={onClick}
      onMouseLeave={() => {
        setShow(false);
      }}
    >
      {children}
    </Button>
  ) : (
    <Transparent
      onMouseEnter={() => {
        setShow(true);
      }}
    />
  );
}

const Transparent = styled.div`
  width: 10rem;
  position: absolute;
  z-index: 999;
  height: 50rem;
`;

const Button = styled.button`
  cursor: pointer;
  color: white;
  cursor: pointer;
`;

export function LeftArrow() {
  const { scrollPrev } = React.useContext(VisibilityContext);
  return <Left onClick={() => scrollPrev()}>←</Left>;
}
```

모든 코드를 다 넣자니, 코드가 너무 길어져 나름 핵심 부분만 가져왔다.

이 library에서 제공하는 기능 중 제일 마음에 드는것이 바로 VisibilityContext 이다. 즉 해당하는 block이 화면상에서 보이는지 안보이는지 체크를 해주는 기능인데, 이게 Arrow를 사용하는 핵심이 된다.

그리고 장황하게 써두었지만, 사실 핵심 코드는 아래만 보면 된다.

```typescript
export function LeftArrow() {
  const { scrollPrev } = React.useContext(VisibilityContext);
  return <Left onClick={() => scrollPrev()}>←</Left>;
}
```

즉 onClick 이벤트로 클릭을 하면 VisibilityContext 즉 현재 보이지 않는 곳으로 scroll을 움직이게 할 수 있는 설정이다. 이 기능을 좌 우측으로 두어서 깔끔하게 좌우 스크롤바를 구현할 수 있다.

## 완성본

![ezgif-3-ca65f790f8](https://user-images.githubusercontent.com/66371206/178254938-aadfeab6-89fb-4af4-8813-cf2278b31e27.gif)

부드럽게 잘 작동한다..!

만족 ㅎㅎ
