---
layout: post
title: Github Action starter 오류 수정 이야기
subtitle: github action starter-workflows 기여를 했던건에 대하여
categories: TIL
tags: [TIL]
---

## 서론

github action 의 starter workflows는 github의 기본적으로 page를 deploy 할 시 등 가장 기본적으로 사용되는 workflow이다. 아마 많은 FE 개발자 혹은 github pages를 통하여 블로그를 만드시는 분들은 많이들 사용해 봤을 것이라 생각된다.

보통 이러한 workflow는 보통, github deploy 페이지에서 GUI로 선행되어 repository 까지 갈 일이 보통 없다. 그런데, 꽤나 예전에 평소처럼 github page에서 Next.js deploy를 하였는데 문제가 생겼던 경험이 있다. 해당 문제를 타고보니 starter-workflows 에서 최신 Next.js를 지원하지 않아서 문제가 발생했고, 현재 수정이 되었는데, 해결과정을 서술해보도록 하겠다.

## 문제 파악

우선 문제는 크게 두가지였다.

1. Next.js v.14에서는 node 버전을 공식적으로 node v.18 이상을 사용해야된다 > [참고](https://nextjs.org/docs/pages/building-your-application/upgrading/version-14)
2. next config 에서 default output을 export로 지정하고, static HTML export를 따로 실행하지 않는것으로 바뀌었다 > [참고](https://nextjs.org/docs/app/building-your-application/deploying/static-exports)

해당 두 가지 문제를 해결하기 위해서는 next.config.js 파일도 수정해야 하지만 github action workflow에도 수정이 필수적으로 필요했다.

5개월 전 삽질의 흔적을 보니 로컬에서는 변경 후 잘 작동 했던것으로 보인다.

![image](https://github.com/071yoon/071yoon.github.io/assets/66371206/4d74aac6-1196-4e46-98ed-708d89d34171)

여기서 나의 문제는 해결 되었지만, 아무래도 공식 workflow 에서 수정하지 않으면 나와 비슷한 사람들이 많을 것 같아 repository 까지 들어가보며 확인을 해 보았다.

## 공식적으로 해결해보기

우선 꽤나 많은 사람들이 사용하는 workflow template 인데, 나만 문제가 생겼을거라 생각하지 않아 pull request 를 살펴보니 비슷한 문제들이 많이 있었다.

> 문제를 기술 및 해결한 [PR LINK](https://github.com/actions/starter-workflows/pull/2204)

여기서는 앞서 문제 파악에서의 2번 문제 즉 yaml 에서 static HTML export를 하는 부분을 삭제 해주기는 하였지만, node 버전 수정을 하지는 않았다. 이 부분은 편하게 댓글을 달아 수정 요청사항을 추가해서 반영토록 도와주웠다.

<img width="933" alt="image" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/e43305c9-5436-4e79-849d-ba99e51a33ad">

그리고 무려 PR이 올라간지 5개월이 지난 현재 잘 수정되었다고 메일이 왔다.

<img width="952" alt="image" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/6af5eb93-4eec-4c66-a4c9-e5c73f0fb430">

한가지 아쉬웠던 점은, 따로 PR로 빼서 날렸으면 나도 contributor badge를 받을 수 있었을텐데, 귀찮아서 댓글만 남기고 넘어갔던 것 정도,,

> 그리고 진짜 간단한 PR인데, 되게 여유롭게 일 하시는듯

여튼 좋은 일 하나 하기 성공
