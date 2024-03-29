---
layout: post
title: Fix Error via APM
subtitle: APM으로 오류 분석 및 해결하기
categories: TIL
tags: [TIL]
---

## APM 으로 오류 분석 및 해결을 해보자

### 문제 시점

우리 서비스에는 new-relic 이라는 APM이 연동되어 있다. 하지만 사용법이 너무 복잡하고 어려워서 잘 사용하고 있지는 않고 있는데, 마침 쓸 상황이 왔다.

![스크린샷 2022-10-09 오후 9 32 55](https://user-images.githubusercontent.com/66371206/194757039-4ca5891c-381c-4f84-aebd-6770ced5dee4.png)
![스크린샷 2022-10-09 오후 9 32 59](https://user-images.githubusercontent.com/66371206/194757036-bb7040a6-092c-4728-a8a2-e15547395f4c.png)

친구랑 같이 사용중이였는데, 친구의 어플리케이션이 사파리에 접속하자마자 튕겼다는것!
하지만 콘솔창을 열어보지 않아서 어디가 문제인지 확인을 할 수 없는 상황이였다.

연동한 APM을 활용하여 오류를 고쳐보도록 하자!

### 문제 분석

우선 new relic의 browser -> 내가 보려는 Application으로 들어가자

<img width="966" alt="image" src="https://user-images.githubusercontent.com/66371206/194757160-df3694ff-ae98-492f-b17f-5a3cf60a9326.png">

다음 JS errors에서 어떠한 오류들이 났는지 확인해보자

<img width="858" alt="image" src="https://user-images.githubusercontent.com/66371206/194757201-787c1172-8e00-4863-b9aa-f75bbf512c6c.png">

근데 이미 오류가 한 두가지 아니다... 우선 제일 많은 오류가 난 too many re-renders를 확인해보자

<img width="1175" alt="image" src="https://user-images.githubusercontent.com/66371206/194757261-60300485-2a82-40ef-903e-d8a5c53a55a0.png">

음... 이 부분은 localhost 에서 에러가 난 것으로 보아 아마 그냥 팀원이나 내가 테스트를 하다 잘못 걸려 들어왔던 것 같다.

나머지도 확인해보니 거의 localhost 작업시에 나왔던 에러로 보이다,,, 드디어 문제를 찾았다

<img width="1151" alt="image" src="https://user-images.githubusercontent.com/66371206/194757389-4a464c5c-6caa-4415-b197-1ab64c137bae.png">

약 3시간 전, modocode.com 즉 production 사이트에서 Safari오류가 난 것을 확인했다.

`navigator.permissions.query` 가 왜 문제일까?

혹시 버전의 문제일까 싶어 MDN 문서를 확인해보았다.

<img width="827" alt="image" src="https://user-images.githubusercontent.com/66371206/194757590-c09efd79-e1e1-4f49-af57-c20f5b6a2a14.png">

문서상으로는 fully compatible 이라고 나오기는 한다. 하지만 잘 보면 접속했던 Safari 브라우저는 15.5 이고, 16부터 호환이 된다고 나와있다. 그럼 혹시 15.5에서는 navigator.permissions.query가 작동하지 않는게 아닐까?

내가 좋아하는 다른 웹사이트 `caniuse`를 확인해보자.

<img width="1449" alt="image" src="https://user-images.githubusercontent.com/66371206/194757641-d4297db0-eba7-4ce1-ac5c-dc310e27bd21.png">

음 문서를 보니 정확하게 15.6 까지 호환이 안되고 그 이후가 호환이 된다고 나와있다.

이제 오류를 찾고 원인도 분석하였으니 해결해보자

### 문제 해결

사실 이번 오류가 난 기능은 그렇게 중요한 기능은 아니다. 내가 방에 입장하기 전, 마이크 혹은 카메라의 permission이 있는지 확인하게 2단계 보안 장치라 볼 수 있다. 하지만, 생각보다 지원하지 않는 버전과 브라우저가 꽤나 있고, 유저가 어떠한 브라우저를 사용하는지는 알 수 없으므로, 만약 지원이 되지 않는 브라우저는 과감하게 해당 기능을 제외하기로 결심했다.

```typescript
const checkValid = !!navigator.permissions?.query;
if (checkValid) {
  navigator.permissions.query({ name: permissionName }).then((result) => {
    if (result.state === "granted") {
      setDisableButton(false);
    } else if (result.state === "prompt") {
      setDisableButton(true);
    } else if (result.state === "denied") {
      setDisableButton(false);
    }
  });
}
```

그냥 boolean으로, 내가 permissions.query라는 함수가 있을 때는 원하는 기능을 동작하고, 아닌 경우 무시할 수 있도록 설정해주었다.

## 후기

사실 문제 자체는 정말 간단하고, 해결도 쉽게 하였지만 만약 APM이 없었다면 생각보다 오래 걸렸을 것이다. 어떤 브라우저 어떤 버전에 따라 어떤 문제가 생길지도 모르는 법이고, 이러한 경우의 수를 모두 테스트해가며 코드를 완성시킬 수 없기 때문이다. APM 때문에, light house 점수도 많이 깎이고 이걸 왜 써야되지... 싶었던 적도 있었지만 해당 기술을 가지고 문제 해결을 하니 기분이 좋다 ㅎㅎ

> 세상에 쓸모 없는것은 없다
