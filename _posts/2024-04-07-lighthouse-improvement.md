---
layout: post
title: Lighthouse 점수 개선기
subtitle: light house 점수를 올려보자
categories: web
tags: [web]
---

## 배경

내 정보를 보여주는 웹사이트를 대충 만든지 8개월이 지났다. 가끔 자격증을 따거나 업데이트 소식이 있을 때 사용했었는데, 오랜만에 들어가서 보니 light house 점수가 말이 아니였다.

여유로운 주말에 점수나 올려보자 시작된 웹 개선기.

## 수정 사항

처음에 모바일도 고려해서 만들었기에, CLS 같은 류는 점수가 그렇게 낮지는 않았다. 그래서 우선 light house를 한번 돌려본 후 알려주는 수정사항만 추가해보았다.

### Sufficient Contrast Ratio

글을 배경에 비해서 contrast 가 존재해야 되는데, 흰 배경에 회식글을 작성해서 생긴 문제였다. 디자이너가 없는 사이트였기에, 나 혼자 디자인이랍시고 회색으로 a tag를 만들었었는데, 구글을 마음에 들지 않았나보다. 적당히 색을 검정에 가까운 회색으로 수정하여 해결

### Links must have discernible text

링크에 label이 없어서 생긴 문제. 각 링크별로 label로 어떤 역할을 하는지 알려줘야 링크에 대한 속성을 알 수 있어 screen leader가 읽을 수 있는데, 적지 않아서 생긴 문제. 예전에 image에 대한 것들은 alt prop으로 추가해두었는데, 링크에 대한 부분은 그냥 a tag 내부에 있는걸 읽을것으로 예상해서 문제가 없을 줄 알았는데 틀렸나보다.

a tag에 aria label 속성을 추가하여 해결

### Console에 나오는 에러 해결

이거는 light house 보다 Next.js에서 warning 이나 error가 나오는 부분이 console에 찍히고, 개선하라고 나온 문제였다.

오류 자체는 Next.js 13 이상의 이미지에서는 style prop을 지원하기에, 원래 Image tag에 바로 적용했던 style 속성을 Image -> style -> prop 으로 수정해서 해결

### LCP

LCP(Largest Content Paint)는 웹 화면의 첫 로딩 속도에 문제가 생길 경우 낮게 점수가 나온다. 보통 이미지의 사이즈가 큰 경우 로딩에 문제가 생기고, 마찬가지로 나도 사이트에 gif 이미지를 넣어, 로딩이 오래 걸렸었다. 해당 문제는 Next.js image의 priority 속성을 추가해 높은 우선순위를 주어 LCP 의 로딩속도를 개선하여 해결

### SEO

링크만 타고 올거라 예상한 사이트였기에, 따로 SEO 설정을 하지 않았는데, 점수가 낮게 나와 간단하게 robots.txt 와 sitemap.xml을 추가하여 해결

## 결과

사실 light house는 본인의 컴퓨터 성능이나, 웹 캐시 등 다양한 이슈에 따라 점수가 다르기에 절대적인 지표는 절대 아니다.

내 사이트 또한 로컬 배포된 환경에서 실행하고 light house에서 알려주는 문제만 해결했기에, 완벽하게 최적화된 사이트라고 볼 수는 없다. 하지만 우선 높은 점수를 받은걸로 만족하고, 다음에는 조금 더 절대적인 사이트 최적화를 진행해보도록 하자.

<img width="593" alt="스크린샷 2024-04-07 오후 12 25 19" src="https://github.com/071yoon/071yoon.github.io/assets/66371206/840de045-79eb-43f8-be16-be548cd7d699">
