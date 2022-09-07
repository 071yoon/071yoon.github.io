---
layout: post
title: React에서 Volume Slider 구현하기
subtitle: React Volume Slider
categories: modoco
tags: [TIL, React]
---

## React 볼륨 슬라이더 만들기

프로젝트를 진행중에, 이러한 시안이 날라왔다. 대충 볼륨을 컨트롤 할 수 있는 Slider를 제작하는 것.

<img width="758" alt="image" src="https://user-images.githubusercontent.com/66371206/182016262-70befb1d-aa0f-4e68-b26d-4c61d20f1199.png">

우선 여러가지 선택사항이 있는데, material UI를 사용하여, 간단하고 이쁜 슬라이더를 가져다 쓰는것, 아니면 직접 구현이 있었다.

우선 라이브러리를 쓰는것 보다 구현을 하는것을 좋아하기에, 한번 알아보았고, `input type` 을 `range`로 선택하여 간단하게 구현할 수 있음을 알게되었다.

### Vanilla HTML로 구현

```typescript
<input
  type="range"
  min={0}
  max={1}
  color="gray"
  step={0.02}
  value={volume}
  onChange={(event) => {
    setVolume(event.target.valueAsNumber);
  }}
/>
```

간단하게 구현을 해보면 위의 코드와 같다.

setVolume같은 경우에는, state로 관리하기 위해서 넣었고, step 기준으로 volume bar를 컨트롤 할 수 있다.

![ezgif-3-5e9fa8f258](https://user-images.githubusercontent.com/66371206/182019180-9500a561-ee79-468d-b97b-220abb93d398.gif)

괜찮아 보이지만, 여러 문제가 있다.

1. 컨트롤 하는 동그라미 (thumb) 도 파란색이 마음에 안든다
2. 볼륨이 0이 됐을 때 투명해졌으면 좋겠다
3. 볼륨이 올라갈때 색이 파란색이 마음에 안든다

이러한 문제를 해결하기 위해 CSS에서 노력을 해주어야 한다.

### CSS 적용

```typescript
<VolumeControl volume={volume * 100} speaker={userSpeaker}>
  <input
    type="range"
    min={0}
    max={1}
    step={0.02}
    value={volume}
    onChange={(event) => {
      setVolume(event.target.valueAsNumber);
    }}
  />
</VolumeControl>
```

우선 styled로 크게 VolumeControl이라는 component로 분할하였고, speaker로 스피커가 꺼져있음을 판단할 수 있고, volume으로 나의 speaker 크기를 판단할 수 있게 만들었다. 여기서 `volume === 0` 인 경우 speaker 가 꺼져있다고 판단할것이다.

#### 컨트롤 하는 동그라미 (thumb) 도 파란색이 마음에 안든다

우선 처음에는 1번 문제를 해결하기 위해 slider-thumb를 손봐주었다.

```css
&::-webkit-slider-thumb {
      -webkit-appearance: none;
      height: 16px;
      width: 16px;
      border-radius: 50%;
      background: ${(props) => (props.volume ? "#d9d9d9" : "#E5E7EB")};
      margin-top: -5px;
      cursor: pointer;
    }
```

해당코드는 별 문제 없이 가능하다. 그냥 volume 이 0이라면 볼륨이 꺼졌을 때의 색상이 나올 수 있게 지정하고 끝이다.

#### 볼륨이 0이 됐을 때 투명해졌으면 좋겠다

이부분도 그렇게 어렵지 않다.

```css
    &::-webkit-slider-runnable-track {
      height: 0.6rem;
      opacity: ${(props) => (props.volume && props.speaker ? "1" : "0.5")};
    }
```

그냥 opacity를 volume과 speaker가 존재 시 1, 아니면 0.5를 줘서 간단히 해결하였다.

#### 볼륨이 올라갈때 색이 파란색이 마음에 안든다

이부분이 상당히 애를 먹었다. 우선 여러번의 삽질과 해결법이 존재하였고, 나는 나름 정말 우아한 해결법을 찾았다고 생각한다.

우선 첫번째는 너무나 쉬운 material-UI 를 사용하는것이다. 하지만, 이미 돌아가기엔, 너무 많이 구현을 해버린 나... 끝까지 바닐라로 해결해보았다.

두번째는 내장 CSS 옵션을 사용하는 방법. CSS 는 브라우저에 의존적이기에, `fill-lower, fill-upper` 를 사용하여 색 지정이 되는 브라우저가 있다고 한다. 하지만 이건 IE만 지원을 하며, 크롬이나 파이어폭스는 지원하지 않아, 무시하였다.

혹시라도 사용하고 싶다면

```css
input[type="range"]::-ms-fill-lower {
  background-color: red;
}

input[type="range"]::-ms-fill-upper {
  background-color: blue;
}
```

이러한 방식을 사용하면 된다.

세번째는 인터넷에 떠돌아 다니는 방법을 사용하는 것. 바로 thumb의 box-shadow를 두어 0부터 thumb까지 전부 색칠하는 방법이다. 그리고 남는 부분은 input 전체에 `overflow: hidden`을 두어 새어 나오는것을 방지하는 방법이다.

```css
input[type="range"] {
  overflow: hidden;
  width: 80px;
  -webkit-appearance: none;
  background-color: #9a905d;
}

input[type="range"]::-webkit-slider-runnable-track {
  height: 10px;
  -webkit-appearance: none;
  color: #13bba4;
  margin-top: -1px;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 10px;
  height: 10px;
  background: #434343;
  box-shadow: -80px 0 0 80px #43e5f7;
}
```

이건 괜찮아 보이지만 문제가 많다. 우선 완성된 결과물부터 보자.

![ezgif-3-875a95cf4c](https://user-images.githubusercontent.com/66371206/182020050-bbbdd186-67b7-45b0-849f-fc0eec3c075b.gif)

우선 box shadow를 활용하기에, 동그라미인 thumb 를 사용할 수 없으며 overflow-hidden이 필수적으로 필요해 원하는 이쁜 디자인을 절대 만들 수 없다. 그래서 더 고민을 하고 linear-gradient를 활용한 우아한 해결법을 찾았다.

linear-gradient 가 단순히 서서히 적용되지 않고, 완전 다른 색으로 hard line을 그릴 수 있는 방법을 이용하였다. 코드를 보면 다음과 같다.

```css
background: ${(props) =>
        props.volume
          ? `linear-gradient(to right, #D9D9D9 ${props.volume}%, rgba(229, 231, 235, 0.5)
 ${props.volume}% 100%)`
          : "#E5E7EB"};
```

우선 볼륨을 0 ~ 100 의 수를 주므로, 해당하는 볼륨 % 까지, `#d9d9d9`의 색으로, 그 다음부터는 rgba(229, 231, 235, 0.5) 에 해당하는 색으로 지정하였다. 그렇게 완성된 볼륨 슬라이더는 다음과 같다.

![ezgif-3-10012500c9](https://user-images.githubusercontent.com/66371206/182032799-34a4b224-7e92-4388-8d45-58db2714bad0.gif)

> 최종적으로 적용된 CSS 는 다음과 같다.

```typescript
const VolumeControl = styled.div<{ volume: number; speaker: boolean }>`
  display: flex;
  justify-content: center;
  align-items: center;
  margin-left: 1rem;
  width: 6rem;

  input[type='range'] {
    -webkit-appearance: none;
    height: 100%;
    background: transparent;

    &:focus {
      outline: none;
    }

    //WEBKIT
    &::-webkit-slider-thumb {
      -webkit-appearance: none;
      height: 16px;
      width: 16px;
      border-radius: 50%;
      background: ${(props) => (props.volume ? "#d9d9d9" : "#E5E7EB")};
      margin-top: -5px;
      cursor: pointer;
    }

    &::-webkit-slider-runnable-track {
      height: 0.6rem;
      background: ${(props) =>
        props.volume
          ? `linear-gradient(to right, #D9D9D9 ${props.volume}%, rgba(229, 231, 235, 0.5)
 ${props.volume}% 100%)`
          : "#E5E7EB"};
      opacity: ${(props) => (props.volume && props.speaker ? "1" : "0.5")};
      border-radius: 3rem;
      transition: all 0.5s;
      cursor: pointer;
    }
`;
```
