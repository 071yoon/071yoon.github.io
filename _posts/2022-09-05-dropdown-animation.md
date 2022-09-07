---
layout: post
title: 드랍다운 애니메이션 제작
subtitle: 커스텀 드랍다운 애니메이션을 만들어보자
categories: blog
tags: [css, javascript]
---

![ezgif-4-8e32a6168e](https://user-images.githubusercontent.com/66371206/188813661-d31a95d8-8a3d-4916-a82e-8a924d5010f2.gif)

## Why?

나만의 드랍다운을 만들어보자.

사실 커스텀 드랍다운은 옛날부터 만들고 싶었지만, 시간 투자 대비 사소한 디자인이여서 그냥 기본 custom selector를 주로 사용했었다.
그러다 이제는 더이상 미룰 수 없기에 커스텀 드랍다운 제작에 나섰다.
우선 이번 드랍다운의 기능은 다음과 같다.

1. 드랍다운 메뉴를 클릭하면, 드랍다운 메뉴(현재 내가 선택 가능한 입출력 디바이스)가 나타난다.
2. 드랍다운 메뉴를 다시 클릭 혹은 select하면, 드랍다운 메뉴가 사라진다.
3. select된 입출력 디바이스는 해당 브라우저 탭에서 오디오 입출력 디바이스가 된다.

입출력 디바이스를 선택하는 부분은, 따로 라이브러리를 사용하지 않고, 브라우저의 MediaDevices API를 사용하여 구현하였다.
내용이 생각보다 길어 다음 포스트에 이어서 작성하겠다.

### 드랍다운 프레임 만들기

드랍다운을 만들기 전에, 먼저 디자인을 해보자.

우선 어떠한 버튼을 누르면 DropDown이 나오게 될지 만들어야 된다.

```typescript
const dropDown = (event: React.MouseEvent<HTMLDivElement>) => {
  event.preventDefault();
  setDropDown(!showDropDown);
};

<DropDownContainer onClick={dropDown}>
  <Text>{myDevice !== null ? myDevice?.label : defaultDevice(device)}</Text>
  <BottomArrow />
</DropDownContainer>;
```

여기서 DropDownContainer를 누르면, !setDropDown을 해주어, 껐다 킬 수 있는 toggle을 만들어 주었다. 그렇게 되면, 애니메이션이 없는 누르면 껐다 켜질 수 있는 토글 버튼이 만들어 지게 된다.

그 다음에, `DropDownItem`에 내가 원하는 디바이스의 아이템들을 이쁘게 담아둔다.

```typescript
const setDevice = (
  device: MediaDeviceInfo,
  event: React.MouseEvent<HTMLDivElement>
) => {
  event.preventDefault();
  setMyDevice(device);
  setDropDown(false);
};

<DropDownItem
  key={device.deviceId}
  onClick={(event) => {
    setDevice(device, event);
  }}
>
  <Text>{device.label}</Text>
</DropDownItem>;

const DropDownItem = styled.div`
  border-radius: 0.5rem;
  padding: 1rem;
  cursor: pointer;
  &:hover {
    background-color: rgba(255, 255, 255, 0.1);
  }
`;
```

DropDown을 클릭하게 되면, 현재 내가 선택한 디바이스state로 설정한 후 추후에 state변화를 감지하여, 입출력 디바이스로 변경할 것이다. 그리고, 자연스럽게 DropDown을 닫아준다.
디자인적으로 `Text`로 디바이스의 이름을 이쁘게 출력할 수 있게 설정해주었다.

![ezgif-4-76b3c89864](https://user-images.githubusercontent.com/66371206/188814527-971f756d-5b06-4b7f-b2ed-0004c1465103.gif)

이렇게 되면, 애니메이션 없는 드랍다운이 완성된다. 하지만 내가 원하는건 그저 토글이 되는 드랍다운이 아닌, 밑으로 죽 내려오는 드랍다운이다.

### 드랍다운 애니메이션 만들기

여기서 내적 고민을 잠깐 하였다... 간단하게 Bootstrap혹은 이미 있는걸 가져다 사용할 지 아니면 직접 CSS로 완성할지... 그러다 내가 원하는 디자인 및 감성으로 제작하기 위해 커스텀 DropDown을 만들게 되었다.

생각보다 자료도 많이 없고, 아이디어를 생각해내기 쉽지 않았다. 그러다 생각해낸 방법은, 드랍다운이 내려오는 창 바로 위에 선을 하나 두고 `overflow: hidden`으로 감싼 뒤 toggle되면 드랍다운을 내리는 animation transition을 넣는 방식이였다.

animation은 간단하게 keyframes 로 구성하였다

```typescript
@keyframes dropdown {
    0% {
      transform: translateY(-100%);
    }
    100% {
      transform: translateY(0);
    }
  }
  animation: dropdown 0.4s ease;
```

영상으로 보는것이 이해가 쉬울 것 같아 가져왔다.

![ezgif-4-c2ecb01224](https://user-images.githubusercontent.com/66371206/188813331-8fd6e3ca-6739-429b-84d0-cb6250365424.gif)

보이는것과 같이, 바를 기준으로 드랍다운이 내려오게 설정하였고, `overflow: hidden` 을 하지 않았을 때 상황이다.

이 상태에서, `overflow` 를 가려서 위에 부분을 안보이게만 하면 이쁜 드랍다운이 완성된다.

주요 코드는 다음과 같다.

```typescript
<DropDownContainer onClick={dropDown}>
  <Text>{myDevice !== null ? myDevice?.label : defaultDevice(device)}</Text>
  <BottomArrow />
  {showDropDown && (
    <DropDownPosition>
      <DropDown>
        {device.map((device) => (
          <DropDownItem
            key={device.deviceId}
            onClick={(event) => {
              setDevice(device, event);
            }}
          >
            <Text>{device.label}</Text>
          </DropDownItem>
        ))}
      </DropDown>
    </DropDownPosition>
  )}
</DropDownContainer>;

const DropDownPosition = styled.div`
  position: absolute;
  top: 5rem;
  left: 0;
  width: 100%;
  overflow: hidden;
  z-index: 1;
`;

const DropDown = styled.div`
  border-radius: 0.5rem;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  background-color: #31343e;
  @keyframes dropdown {
    0% {
      transform: translateY(-100%);
    }
    100% {
      transform: translateY(0);
    }
  }
  animation: dropdown 0.4s ease;
`;

const DropDownItem = styled.div`
  border-radius: 0.5rem;
  padding: 1rem;
  cursor: pointer;
  &:hover {
    background-color: rgba(255, 255, 255, 0.1);
  }
`;
```

완성된 버전

![ezgif-4-8e32a6168e](https://user-images.githubusercontent.com/66371206/188813661-d31a95d8-8a3d-4916-a82e-8a924d5010f2.gif)
