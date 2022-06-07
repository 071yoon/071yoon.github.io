---
layout: post
title: Mac 초기 세팅
subtitle: 맥 새로 개봉 시 다운받는 Tools
categories: Mac
tags: [Mac, Setting]
---

## 나만의 맥북 셋업

이번에 새로운 Mac m1 pro 를 갖게 되면서, 설치하다보니 뭐를 설치해야되고 어떤거를 동기화 해야되고 생각보다 기억이 잘 안났다. 그래서 이번 기회에 나의 맥이라면 이런걸 해야하는 TODO List를 만들어 보았다.

### 개발자 도구

- [iTerm2](#iterm-setting)
  - [Oh-my-zsh](#oh-my-zsh)
  - [Powerlevel10k](#powerlevel10k)
  - [brew](#brew)
  - [git-setup](#git-setup)
- [Web](#web-programming)
  - [node](#node)
  - [yarn](#yarn)
- [Blog](#blog)
  - [ruby](#ruby)
  - [bundler&jekyll](#bundle--jekyll)

### 어플

- [Chrome](#chrome)
- [Visual Stuido Code](#visual-stuido-code)
- [Discord](#discord)
- [KakaoTalk](#kakaotalk)
- [Webex](#webex)
- [Slack](#slack)
- [Notion](#notion)
- [Terminus](#terminus)

### 위젯

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

- #### Styled-Components

styled에서 주석처럼 안달리고, 코드처럼 사용가능. 훨씬 쉽다

- #### Material Icon

옆에 아이콘들 이쁘게 보임

- #### Markdown All in One

블로그 쓸 일이 많다면, 마크다운 쓸 때 도움 받을 수 있다

- #### Markdown Preview

마크다운 미리보기 지원

### Discord

### KakaoTalk

### Webex

### Slack

### Notion

### Terminus

## Widgets

### Alt-Tab

### Fig

### RunCat

### Unicorn

### Hidden-Bar

### Magnet

### Karabiner

### Amphetamine
