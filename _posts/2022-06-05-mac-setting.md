---
layout: post
title: Mac 초기 세팅
subtitle: 맥 새로 개봉 시 다운받는 Tools
categories: Mac
tags: [Mac, Setting]
---

## 나만의 맥북 셋업

이번에 새로운 Mac m1 pro 를 갖게 되면서, 설치하다보니 뭐를 설치해야되고 어떤거를 동기화 해야되고 생각보다 기억이 잘 안났다. 그래서 이번 기회에 나의 맥이라면 이런걸 해야하는 TODO List를 만들어 보았다.

### [개발자 도구](#개발자도구)

- [iTerm2](#iterm-setting)
- [Web](#web-programming)
- [Blog](#blog)

### [어플](#applications)

- [Chrome](#chrome)
- [Visual Stuido Code](#visual-stuido-code)
- [Discord](#discord)
- [KakaoTalk](#kakaotalk)
- [Webex](#webex)
- [Slack](#slack)
- [Notion](#notion)
- [Terminus](#terminus)

### [위젯](#Widgets)

- [Alt-Tab](#alt-tab)
- [Fig](#fig)
- [RunCat](#runcat)
- [Unicorn](#unicorn)
- [Hidden-Bar](#hidden-bar)
- [Magnet](#magnet)
- [Karabiner](#karabiner)
- [Amphetamine](#amphetamine)

## 개발자도구

### iTerm Setting

나는 내장 terminal보다 iTerm을 주로 쓴다. 하이라이트, 복사, 붙여넣기, 히스토리 등 다양한 기능이 많아서 더 편함

- #### Oh-my-zsh

  기왕 Terminal 쓰는김에 이쁘면 좋잖아요?

  [공식문서](https://ohmyz.sh)

- #### Powerlevel10k

  마찬가지로 zsh shell을 더 이쁘게 만들어준다.

  설치 끝내고, VSC Terminal에서도 폰트 적용을 해서 깨지는걸 방지하자

  [공식문서](https://github.com/romkatv/powerlevel10k)
  [도움받은 블로그](https://velog.io/@boms2/Mac-Powerlevel10k로-Zsh-터미널-꾸미기-M1)

  완성된 모습
  <img width="681" alt="스크린샷 2022-06-09 오후 2 00 03" src="https://user-images.githubusercontent.com/66371206/172768037-5da27996-7652-42ff-874b-f84c55648b32.png">

- #### brew

  당연히 깔아야 하는 맥주

  [공식문서](https://brew.sh/index_ko)

- #### git setup

  git login을 미리미리 해두자

  ```shell
  $ git config --global user.name "John Doe"
  $ git config --global user.email johndoe@example.com
  ```

### Web Programming

명색이 프론트엔드이기에 깔아야 하는 기본적인 세팅

- #### node

  맥주가 있어 행복하다

  ```shell
  brew install node
  node --version
  npm --version
  ```

- #### yarn

  npm보다 더 안전하고 오류를 잘 잡아준다

  ```shell
  brew install yarn --ignore-dependencies
  ```

### Blog

해당 깃허브 블로그 또한 jekyll 로 관리하기에 ruby, bundle, jekyll설치가 필수적이다

- #### Ruby

  ```shell
  brew install rbenv ruby-build
  rbenv install -l #버전은 알아서 선택! 근데 3.0.0 부터 잘 안됐던거 같음
  rbenv install 2.7.2
  rbenv global 2.7.2
  ```

  zsh 적용하기

  ```shell
  vim ~/.zshrc
  ```

  들어가서 환경변수 추가

  ```shell
  export PATH="$HOME/.rbenv/bin:$PATH"
  eval "$(rbenv init -)"
  ```

- #### Bundle && Jekyll

  ```shell
  gem install bundler
  bundle add jekyll
  bundle install
  gem install jekyll
  ```

## Applications

내가 최초로 설치했던 어플리케이션 목록

### Chrome

사파리 대체용품

### Visual Stuido Code

- #### Code Runner

  작성한 코드를 빠르게 실행시켜주게 도와주는 확장

- #### Discord Presence

  Discord 에서 내가 어떤 작업하고 있는지 보여주는 확장
  <img width="353" alt="스크린샷 2022-06-09 오후 2 02 14" src="https://user-images.githubusercontent.com/66371206/172768189-80f3ab3c-8d3a-4ff7-98ff-a30175ceed47.png">

- #### ESLint

  ESLint 규칙 적용해주는 확장

- #### Git-Graph

  VSC Git Page에서 Git-Kraken 처럼 어떤 브랜치에서 어떤 작업하는지 보여주는 확장

- #### Live Share

  VSC를 쓰는 주된 이유

- #### Live Server

  VSC를 쓰는 주된 이유2 -> Live share 호스트 컴퓨터에서 서버 올릴 때 접속한 사람들도 같은 localhost로 접속 할 수 있음

- #### Prettier

  Lint 혹은 일반적인 코드 포맷팅 도와주는 포매터
  기본 foramtter를 prettier로 설정 및 format on save 등록하는걸 까먹지 말자.

  ![image](https://user-images.githubusercontent.com/66371206/172768418-1d71bd54-8a8d-4faf-8a08-0aef05dc6d74.png)

  ![image](https://user-images.githubusercontent.com/66371206/172768450-f4fc69bc-5ba7-4a13-8cf4-e3788dfd480d.png)

- #### Styled-Components

  styled에서 주석처럼 안달리고, 코드처럼 사용가능. 훨씬 쉽다.
  심지어 추천도 해준다
  ![image](https://user-images.githubusercontent.com/66371206/172768289-34e830cd-3a5b-472e-87a0-e2e6dd12f0d1.png)

- #### Material Icon

  ![image](https://user-images.githubusercontent.com/66371206/172767754-dee7ea7e-5c56-485d-aae3-8cce8d3cf2af.png)

  옆에 아이콘들 이쁘게 보임

- #### Markdown All in One

  블로그 쓸 일이 많다면, 마크다운 쓸 때 도움 받을 수 있다

- #### Markdown Preview

  마크다운 미리보기 지원

  ![image](https://user-images.githubusercontent.com/66371206/172768531-8acc01b2-0d48-4926-81b1-c780fa316a20.png)

### Discord

### KakaoTalk

### Webex

### Slack

### Notion

### Terminus

## Widgets

### Alt-Tab

보통 맥북을 사용하다보면, Cmd + Tab을 주로 사용하는데, 사파리 창이 여러개 띄워져 있는 경우에 상당히 불편하다. 윈도우처럼 쓰는 창들이 전부 분할해서 나오면 참 좋겠다~ 싶을 때 사용하는 위젯

<img width="1215" alt="image" src="https://user-images.githubusercontent.com/66371206/172768969-3022bcc2-c7d9-42ff-ae94-ad978e8af522.png">

### Fig

터미널을 쓰다보면, 단축키가 뭐였는지 까먹을 때가 많다. 올바른 명령어를 추천해주기도 하고, cd 에서 어디로 가야할 지 모를 때, ls로 안찾고 gui처럼 쉽게 찾을 수 있다.

![스크린샷 2022-06-09 오후 2 12 43](https://user-images.githubusercontent.com/66371206/172769368-c464cf70-019b-4216-8baa-ec91a1d90111.png)

![스크린샷 2022-06-09 오후 2 12 58](https://user-images.githubusercontent.com/66371206/172769373-95a150b5-d293-4589-af72-9cb13196ba90.png)

![스크린샷 2022-06-09 오후 2 13 10](https://user-images.githubusercontent.com/66371206/172769378-86ed108d-4a26-492c-a47d-b90ba30a515b.png)

### RunCat

상태바에 CPU 속도만큼 뛰는 고양이

나는 주로 귀여운 슬라임과 WIFI 다운로드 및 업로드 속도를 켜둔다

![화면-기록-2022-06-09-오후-2 14 41](https://user-images.githubusercontent.com/66371206/172769669-0f0e2982-bf7f-4395-8fd8-a8fe1384ead2.gif)

### Unicorn

사파리 광고차단 위젯.

유료인데 값어치를 한다고 생각한다

### Hidden-Bar

상태바에 너무 많아서 정리하고 싶을 때 사용.

보통 상태바에서 정말 빠져도 괜찮다~ 싶은데, 필수록 달아둬야 하는게 많은데, 그런것들 빼놓을 수 있다

### Magnet

### Karabiner

윈도우 키보드를 맥용으로 사용할 수 있게 도와준다.

나는 Cmd와 Option 키만 바꿔서 사용한다.

### Amphetamine
