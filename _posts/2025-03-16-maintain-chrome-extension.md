---
layout: post
title: Chrome Web Store 유지보수
subtitle: Web Store Extension 유지보수 이야기
categories: web
tags: [web, extension]
---

## Chrome Extension Console

예전에 크롬 웹 익스텐션을 개발한 적이 있다.
처음에는 단순히 게임하면서 내가 활용하려고 개발하였다가, 같이 쓸 수 있으면 좋겠다고 생각해서 공유를 했었다.

> [블로그 링크](https://071yoon.github.io/til/2023/09/18/make-chomre-web-extension.html) 는 여기서

그러던 어느날, 그냥 extension 이 잘 살아있는지? 사람들은 잘 쓰고 있는지 궁금해서 Web Extension Console 에 들어가서 한번 통계를 살펴 봤었다.

그런데 웬걸, 생각보다 내가 개발한 익스텐션이 검색에 상위 노출이 되었고, 지속적으로 다운로드가 되고 있었다.

나름 Auto Scroll Extension 이라고 치면 상위 3등으로 검색이 되어 있었다.
<img width="1345" alt="Image" src="https://github.com/user-attachments/assets/0e077026-83e1-4a0a-982b-dcc0ea1bddab" />

여튼 그런데 통계를 보니까 처음에는 한국인 기준으로 개발을 했었는데, 생각보다 해외에서 다운로드가 많았다.

익스텐션 같은 경우에는 구글에서 승인을 받아야지 올릴 수가 있어서, 설명이나 주석 같은것들을 전부 영어로 작성해서 그래서 외국인 유입이 좀 많았나보다.

여튼 그래서 기왕 상위 노출도 되고, 외국인들도 많이 쓰는것 같으니 조금 유지보수를 해서 영어 지원도 하고 일반 사용자들이 사용하기 쉽게 만들어 보기로 했다.

작년 초 기준 (유지보수 하기 전) 최고 97이고 평균적으로 일일 방문자 30명 정도에 설치수는 10회 정도로 보였다.

<img width="504" alt="Image" src="https://github.com/user-attachments/assets/8cdaccf4-0073-40d4-a6f4-af5e81a48d8b" />

<img width="524" alt="Image" src="https://github.com/user-attachments/assets/2b966638-a59a-496a-9746-eb19e7b46bd6" />

## 유지보수

우선 다국어 지원을 통해 외국인 사용자들이 조금 더 유입이 편하도록 수정하고, 조금더 사용성이 용이할 수 있게 자동 모드 등을 포함시켰다.

> [수정사항 커밋](https://github.com/071yoon/auto-scroll-extension/commit/33a99118b0366ed703338a95d6af968554a68c03)

코드는 조금 날림으로 쓰고 이따 천천히 리팩토링도 하고 수정도 해야지 해야지 하는데 요즘 일에 치이고 시간도 없고 귀찮아서 자꾸만 밀린다... ㅠ

## 비교 분석

여튼 이렇게 수정하고 나니 확실히 조회수나 설치수도 눈에 띄게 늘었다. 일일 조회수는 이제 평균적으로 50 명 정도로 늘었고, 설치수도 20회 정도로 물론 날마다 차이는 있지만, 큰 흐름으로 보았을 때 우상향으로 증가했다.

<img width="555" alt="Image" src="https://github.com/user-attachments/assets/3628ae9c-ade5-4d39-97e3-c056e47db8cd" />

<img width="589" alt="Image" src="https://github.com/user-attachments/assets/fcfa4fef-e0db-4da7-9c50-8d22a0c1c60f" />

특히 1년간 사용자의 증가 추이를 보면, 천천히지만 증가하는 모습을 볼 수 있다. 그리규 유지보수를 진행했던 10월을 기점으로 확실히 사용자가 확 늘어남을 눈으로 확인할 수 있다.

<img width="1087" alt="Image" src="https://github.com/user-attachments/assets/c3956b94-3dc7-46ef-ad05-7772289541d1" />

여기서 google analytics 등록하여 페이지 조회를 하면 더 자세한 내용을 볼 수 있는데, 국가 통계를 보면 다음과 같다.

<img width="1308" alt="Image" src="https://github.com/user-attachments/assets/1881880b-dfc6-4a12-82b4-ea04649abac7" />

아쉽게도 기간으로 보는 법은 없어서 해외 유입이 얼마나 증가했는지는 볼 수 없지만, 예전에 비해서는 정말 해외 비율이 많아졌다.

버저닝으로 사용자가 얼마나 많아졌는지 보는 방법도 있었는데, 해당 그래프틑 다음과 같다.

<img width="735" alt="Image" src="https://github.com/user-attachments/assets/8c439595-0a74-4fc6-8ee3-09b02f778e19" />

1.0.2.0 버전 기준에서 천천히 증가를 하다 (300 - 600 까지 7개월) 업데이트 후 600 - 1200 까지 5개월만에 달성하였다.

## 회고

사실 이때까지 몇가지 프로젝트를 하고, 유지보수를 포기한 프로젝트도 많고 (거의 90%) 정말 공을 들였지만 사람들이 쓰지 않아서 망했던 프로젝트들도 참 많은데, 역시 사람일은 모르는것 같다.

예전에 사이드 프로젝트로 억대 연봉을 버는 사람 말이 생각나는데, `사이드 프로젝트 그냥 그거 일주일에 하나씩 만들다 보면 하나는 대박 터져요` 가 참 맞는 말인것 같다.

그리고 유지보수를 통해서 확실히 통계적으로 수치가 증가하는걸 보면 참 뿌듯하다.. 앞으로 비슷한거 많이 양산해야지 ㅎ.ㅎ
