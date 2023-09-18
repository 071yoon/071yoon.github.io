---
layout: post
title: 크롬 익스텐션을 개발해보자
subtitle: 첫 크롬 익스텐션을 개발해보며
categories: TIL
tags: [web]
---

## 발단

요새 메이플이란 게임에 빠져있었다. 그리고 이 게임에는 `재획` 이라는 시스템이 존재한다. 일반적으로 사냥을 하는 컨텐츠이지만 이 사냥을 많이 하다보면 게임 화면을 보지 않고도 컨텐츠를 즐길 수 있는 경지에 오르게 된다. 보통 이러한 경지를 `뇌빼기`(대충 뇌 빼고도 사냥한다는...) 라고도 하는데, 여기서 메이플의 서브 컨텐츠인 딴거하기를 즐길 수 있다.
보통 이 딴거하기 단계에서는 넷플릭스나 유튜브등 동영상 컨텐츠를 많이 즐기게 된다. 그러던 중 메이플 커뮤니티에서 웹툰을 보는 방법이 올라왔다.
<img width="461" alt="image" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/5b668973-8501-4570-98f0-f8adc7100a67">
사진에 보이듯, f12를 눌러.. 개발자 도구에 들어가.. javascript를 입력해.. 조절을 이렇게... 아마 개발을 하는 나에게는 익숙하지만 일반인에게는 꽤나 복잡하고 이해가 안될거 같다는 생각이 들었다. 그럼 이걸 확장으로 만들면 더 쉽지 않을까? 라고 생각해 바로 개발에 들어갔다.

## 개발

매우 간단한 화면이 되겠지만, 그럼에도 불구하고 React를 심어두면 개발이 편하고 추후에 수정하기도 편할 것 같아, 우선 React + Web Extension을 우선으로 개발하기로 결정했다.

### React 심기

React를 심는것은 기존에 있는 React boilerplate와 유사하다. 다른점은 extension에서 `popup`이란 화면이 메인 화면임이므로 `root` 대신 `popup`에 render해주면 되는것이다.

```javascript
const mountNode = document.getElementById("popup");
ReactDOM.render(<App />, mountNode);
```

그리고 package.json에 필요한 library등을 삽입하여 <App />에서 사용해주면 된다.

### mainfest

manifest는 어떻게 확장을 구성할지에 대한 정의라고 할 수 있다. 일반적으로 그냥 chrome확장에서 제시해주는 대로 작성하면 되지만, 중요한점은 `manifest_version`과 `permissions` 라고 생각한다.

manifest_version은 3이 최신버전이므로 사용하는게 맞지만, 참고 어플들은 2가 많으므로 주의해서 사용하자. 나는 처음에 2로 사용했다가 chrome extension 사이트를 보며 개발하다 outdated가 많아 3으로 올려 버전업을 했다.

permissions는 내 확장에 어떠한 기능들까지 허용해줄지에 대한 정의이다. 나같은 경우에 3가지를 허용해주었는데 하나하나 알아보자.

- `activeTab`
  - 확장이 열린 탭(내가 보고 있는 탭)에 대한 정보를 제공해주겠다는 뜻
- `scripting`
  - 확장에서 script를 실행하게 해주겠다는 뜻
- `storage`
  - chrome web storage를 사용하게 해주겠다는 뜻

### javascript

확장에서 javascript를 실행할때 주의할 점은 node환경이 별개로 두개가 된다는 점이다. 하나는 내가 지금 보고 있는 화면의 javascript와 하나는 확장이 돌아가고 있는 화면의 javascript이다. 각각 content.js와 background.js에서 돌아가게 된다.

하지만 개발을 하다보면 background.js -> content.js로 넘어가게 되는 상황이 많이 생기게 된다. 그럴경우 event listener로 넘겨주며 주고받을 수 있지만, 나는 보다 쉽게 개발하기 위해 바로 확장에서 바로 content의 javascript를 실행할 수 있게 `chrome.scripting.executeScript()`함수를 활용하였다.

예시는 다음과 같다.

```javascript
export const onStart = () => {
  (function () {
    let queryOptions = { active: true, lastFocusedWindow: true };
    chrome.tabs.query(queryOptions, ([tab]) => {
      if (chrome.runtime.lastError) console.error(chrome.runtime.lastError);

      // @ts-ignore
      chrome.scripting.executeScript({
        target: { tabId: tab.id },
        func: (sv: number, st: number) => {
          // do something with sv, st
        },
        args: [scrollValue, scrollTime],
      });
    });
  })();
};
```

### 실행

실행은 `build`를 통해 나온 dist.zip 파일을 `chrome://extensions`에 넣어주고 사용하면 된다. 여기서 조금 불편한점은 매번 update할 때 마다 build를 하고 refresh를 해줘야된다.

## 배포

개발보다 배포가 더 오래 걸렸다. 우선 웹 대시보드 콘솔에 들어가 가입도 해야되고 (가입비 5$도 지불해야된다) 어떤 목적으로 개발했는지, 앞서 허용해주었던 permissions들은 왜 허용을 해주었는지, logo는 잘 등록이 됐는지 등 설명을 많이 해줘야된다. 그래도 나름 콘솔이 잘되어 있어 어떤걸 놓쳤고 미숙한지 잘 알려주기 때문에 나름 따라가기가 쉬웠다.

하지만 나는 설명을 충분히 적지 않아 한번 거부를 당했다..
<img width="505" alt="image" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/267d5e46-2b4b-4f91-9143-a6697d9c9018">

그리고 게시가 되면 다시 메일이 올 줄 알았는데, 게시가 잘 되면 메일이 오지 않고 자동으로 등록됨에 주의..!

## 후기

개발을 완료한 후 당당하게 메이플 커뮤니티에 올렸다. 다행히 반응도 좋았고 사람들이 많이 좋아해줬다. (이것이 나의 개발의 원동력...)
<img width="210" alt="image" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/06c355ec-e44f-426f-991c-efeaa3e656bb">
그리고 다른 커뮤니티에서 까지 퍼가서 홍보해주시는 분도 보았다.. 뿌듯..
다른 커뮤니티에 올라온 나의 글..
<img width="439" alt="스크린샷 2023-09-18 오후 12 22 20" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/af8661fa-d558-427c-864f-0dc3378c3924">
