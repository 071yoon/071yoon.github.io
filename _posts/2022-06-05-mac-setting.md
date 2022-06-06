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

- Chrome
- VSC
  - Code Runner
  - Discord Presence
  - ESLint
  - Git-Graph
  - Live Share
  - Live Server
  - Prettier
  - styled-components
  - Material Icon
  - Markdown All in One
  - Markdown Preview
- Discord
- KakaoTalk
- Webex
- Slack
- Notion
- Terminus

### 위젯

- AltTab
- Fig
- RunCat
- Unicorn
- Hidden Bar
- Magnet
- Karabiner
- Amphetamine

## 개발자도구

### iTerm Setting

나는 내장 terminal보다 iTerm을 주로 쓴다. 하이라이트, 복사, 붙여넣기, 히스토리 등 다양한 기능이 많아서 더 편함

#### Oh-my-zsh

기왕 Terminal 쓰는김에 이쁘면 좋잖아요?

[공식문서](https://ohmyz.sh)

#### Powerlevel10k

마찬가지로 zsh shell을 더 이쁘게 만들어준다.

설치 끝내고, VSC Terminal에서도 폰트 적용을 해서 깨지는걸 방지하자

[공식문서](https://github.com/romkatv/powerlevel10k)
[도움받은 블로그](https://velog.io/@boms2/Mac-Powerlevel10k로-Zsh-터미널-꾸미기-M1)

#### brew

당연히 깔아야 하는 맥주

[공식문서](https://brew.sh/index_ko)

#### git setup

git login을 미리미리 해두자

```shell
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

### Web Programming

명색이 프론트엔드이기에 깔아야 하는 기본적인 세팅

#### node

맥주가 있어 행복하다

```shell
brew install node
node --version
npm --version
```

#### yarn

npm보다 더 안전하고 오류를 잘 잡아준다

```shell
brew install yarn --ignore-dependencies
```

### Blog

해당 깃허브 블로그 또한 jekyll 로 관리하기에 ruby, bundle, jekyll설치가 필수적이다

#### Ruby

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

#### Bundle && Jekyll

```shell
gem install bundler
bundle add jekyll
bundle install
gem install jekyll
```

## Applications

내가 최초로 설치했던 어플리케이션 목록

### Chrome

### Visual Stuido Code

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
