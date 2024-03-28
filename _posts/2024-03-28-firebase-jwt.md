---
layout: post
title: Firebase JWT 에러
subtitle: Firebase JWT를 활용하여 생겼던 에러 수정
categories: web
tags: [web]
---

## 문제 확인

최근 운영하고 있던 서버 중 하나에 정말 큰 문제가 생겼다. 갑자기 모든 화면이 보이지 않는다는것.

현재 DB를 Firebase를 쓰고 있었는데, 혹시나 firebase DB를 너무 많이 써서 초기화를 시켜버리거나 하지는 않았을까? 백업을 안한지 오래됐는데 혹시 파일이 날라가면 어떡하지 두근두근 거리며 확인을 했더니 데이터는 다행히 살아있었고, 즉 firebase 문제는 아니였다.

모든 데이터가 보이지 않고, 작성/수정/삭제 등 모든 기능이 정지되었다. 흠,, 그렇게 되면 사실 데이터를 가져오는 곳에서 문제가 생겼다는 것이고 높은 확률로 fetch 부분이였기에 그부분을 자세히 살펴보았다. 그러던 중 이러한 코드를 발견

```typescript
const auth = (token: string) => {
  const decoded = jwtDecode(token, { header: true });
  if (
    decoded.kid === process.env.NEXT_PUBLIC_FIREBASE_CERT_ONE ||
    decoded.kid === process.env.NEXT_PUBLIC_FIREBASE_CERT_TWO
  ) {
    return true;
  }
  return false;
};
```

꽤나 최근에 작성된 코드였는데, jwt 인증 토큰을 가지고 kid 를 비교해서 올바른 token인지 확인하는 과정이였다.

해당 코드를 작성하게된 계기는 웹사이트 인증수단이 필요했고, 모든 get/update/delete 등 firebase api를 활용할 때 인가를 주기 위해서였다.

그리고 firebase website 내에서도 `마지막으로, 토큰의 kid 클레임에 해당하는 비공개 키로 ID 토큰이 서명되었는지 확인합니다.` 이렇게 작성이 되어 있어, 서명 확인을 조금 단순하게 kid key 값을 가져와 env로 설정하여 진행했었는데, 바로 이 부분에서 문제가 생겼다.

## 해결

앞 코드의 `NEXT_PUBLIC_FIREBASE_CERT_ONE/TWO` 같은 경우는 [google api 사이트](https://www.googleapis.com/robot/v1/metadata/x509/securetoken@system.gserviceaccount.com)에서 직접 가져온 kid 코드였다.

작성 당시에는 문제가 없을거라 생각했는데, google api가 똑똑한것인지 kid 코드를 주기적으로 변경하여 생긴 문제였다. 임시방편으로 웹사이트에 들어가 새로운 kid로 env를 수정하니 생긴 오류는 막을 수 있었다.

다음은 주기적으로 같은 문제가 재발하지 않기위한 코드 수정이 남았다.

수정사항은 간단하다. google api 서버에 접속하여 kid를 실시간으로 가져온 후 jwt decode를 한 kid와 매칭시켜본다. 여기서 추가되는 부분은 `매번 google api 서버에 요청하기보다 한번 가져온 후 재사용할 수 있었으면 좋겠다` 였다.

나 혼자 작성해도 크게 문제될 것 없는 쉬운 문제이지만 reference는 언제나 좋으니 github을 뒤적거리다 좋은 소스를 발견했다.
바로 이름부터 신뢰가 가는 [web-auth-library](https://github.com/kriasoft/web-auth-library/blob/42a722e4cedc09ea2a86f92fe5aed73b19e5de94/google/credentials.ts#L43).

대충 보아하니, auth 인증이 된 유저 instance를 만들어 사용하는 방법인것 같은데, 내 서비스와 살짝 상황이 달랐다. 그래도, 싱글톤 instance로 관리하면 재요청 하지 않고 계속 쓰일 수 있을 것 같아 FirebaseCert Class를 만들어 관리하기로 했다.

```typescript
private async getCertificateFromServer() {
  const response = await fetch(CLIENT_AUTH_URL);
  const data = await response.json();
  this.setCert(data);
}
```

대충 이런식으로 initialize를 1회 해주는 class를 만들어

```typescript
const firebaseCert = new FirebaseCert();

const auth = (token: string) => {
  const decoded = jwtDecode(token, { header: true });
  if (!firebaseCert.getCert() || !decoded.kid) {
    return false;
  }
  const arr = Object.keys(firebaseCert.getCert());
  if (arr.includes(decoded.kid)) {
    return true;
  }
  return false;
};
```

이렇게 사용하도록 수정했다.

테스트해보니 fetch도 최초 1회만 하고, 페이지 전환을 하여도 client 세션이 만료되거나 새로고침 전에는 재 요청 하지 않음을 확인할 수 있었다.

오늘의 문제 해결 끝
