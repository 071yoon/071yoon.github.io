---
layout: post
title: Jira와 깃허브 연동
subtitle: Jira Github 연동 후 스프린트를 뛰어보자
categories: Jira
tags: [Jira, Agile, Github, study]
---

## Jira와 깃허브 연동하기

### 1. 깃허브앱 등록

Jira와 연동을 하려면 우선 깃허브앱을 등록을 해야된다.
<img width="428" alt="image" src="https://user-images.githubusercontent.com/66371206/170931557-3de22400-fb72-4f72-9c0a-55c9d665ef1e.png">

등록에 가서 평점이 조금 낮지만 공식 앱인 `Github for Jira`를 설치한다.

<img width="823" alt="image" src="https://user-images.githubusercontent.com/66371206/170931899-d48cf6c9-57e0-47e7-891d-ce68ac0dc18d.png">

그 후 Authorize를 하고 원하는 Organization 혹은 Repository를 등록하며 진행한다

<img width="574" alt="image" src="https://user-images.githubusercontent.com/66371206/170932096-0d8481f1-b06c-449a-b66d-5a1d1cf658c8.png">

다음과 같이 해당 깃허브 Repository에 Jira 앱이 설치되어 있고, 마찬가지로 Jira에도 앱 추가가 되어있으면 준비가 되었다.

<img width="1156" alt="image" src="https://user-images.githubusercontent.com/66371206/170932363-abe6fda2-4380-4e63-a9d7-01e893ec950d.png">

<img width="1362" alt="image" src="https://user-images.githubusercontent.com/66371206/170932432-2458d419-37dd-479a-9cdb-fc594ce2b415.png">

### 2. 이슈 만들기

우선 이슈를 만들려면 스프린트부터 작성을 해야된다. 해당 지라앱에서 새로운 에픽을 만들어보자.

<img width="410" alt="image" src="https://user-images.githubusercontent.com/66371206/170931358-38c9357e-4599-4387-acb9-fc7569394184.png">

스프린트를 만들었다면, 좌측에 백로그에서 해당 스프린트에 대한 이슈를 만들 수 있다.

<img width="1165" alt="image" src="https://user-images.githubusercontent.com/66371206/170932860-c7c291ae-877a-4a49-86ca-fc6eee95eb64.png">

간단하게 이슈를 하나 만든 후 스프린트 시작을 누르면 시작할 수 있고, 방금 만든 이슈에 대한 내용은 스프린트 페이지에서 하위 이슈로 확인 할 수 있다.

<img width="1174" alt="image" src="https://user-images.githubusercontent.com/66371206/170933380-46979990-b58c-4bf9-b028-68f2912af333.png">

### 3. 브랜치 및 커밋 작성

방금 만든 이슈에 대해서 커밋 만들기, 혹은 브랜치 만들기를 보면 다음과 같이 키를 알려준다.

<img width="411" alt="image" src="https://user-images.githubusercontent.com/66371206/170933552-1f99d427-18ef-4178-8bd4-a61e736f4780.png">

<img width="428" alt="image" src="https://user-images.githubusercontent.com/66371206/170933605-1b00262d-cbae-43b1-869f-3a1bafa70660.png">

여기서 해당하는 키를 가지고 브랜치를 만들고, 커밋을 하면 자동으로 jira 가 해당하는 커밋을 track 해서 연동을 해준다.

그렇게 해서 완성된 이슈를 보면 다음과 같다.

만들기 위해 커밋을 4번, 풀 리퀘스트를 진행 후 merge를 한 이슈이다.

<img width="1169" alt="image" src="https://user-images.githubusercontent.com/66371206/170933764-2d136f8b-36d1-4246-bd46-90dd5682e870.png">

해당하는 FIRE-44에 대한 커밋을 깃허브에서 보면 다음과 같이 설정되어있다.

<img width="1264" alt="image" src="https://user-images.githubusercontent.com/66371206/170933985-8987738a-f687-49a1-af61-dfd22402a387.png">

깃허브와 Jira 연동 끗!!
