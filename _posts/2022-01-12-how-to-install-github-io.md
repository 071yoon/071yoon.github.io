---
layout: post
title: 깃허브 블로그 설치하기
subtitle: MacOS에서 이쁜 깃허브 IO를 설치해보자
categories: blog
tags: [blog, markdown]
---

안녕하세요, `071yoon`입니다.

원래는 노션에서 제가 하는 작업들을 정리해 왔지만, 포스팅용의 사이트가 하나 필요하다 판단되어 Github IO를 채택하기로 했습니다.

혹시라도 제 노션이 궁금하다면 -> [노션링크](https://yeonggi.notion.site/YeongGi-s-Scratch-9085f2628b87451a9e19e655c9c5bd5e "영기의 노션 링크")


## 만드는 과정 **Step by Step**
> MAC 환경에서 작업햇습니다!!!

## 1. 새로운 Repository 생성
> 주의 할 점 -> 이름은 username.github.io 의 형태를 유지해야한다

+ README는 딱히 필요 없다. (근데 어차피 추가 해야됨...)


## 2. `clone`
+ git clone` 으로 만들어 둔 Repiostiry 를 local로 clone

+ 앞으로 해당 디렉토리에서 작업을 한다

## 3. `Jekyll`
> 만약 Windows 환경이라면 Ruby 설치가 필요하다

+ terminal 에서 `gem install jekyll bundler` 로 설치
 
+ 만약 사용하고 싶은 템플릿 가 없다면 `Jekyll`의 기본 템플릿을 사용해보자

+ clone 한 디렉토리에서
  
	```shell
	jekyll new ./
	bundle install 
	bundle exec jekyll serve
	```

  + 까지 하게되면 서버를 열 수 있다

	<img width="695" alt="image" src="https://user-images.githubusercontent.com/66371206/149091259-85fb91e9-e480-4e0d-a521-559517d5c2a3.png">

  + 해당 이미지에서 서버의 Host 주소를 파악 (여기서 http://127.0.0.1:4000/ 인 부분)

  + 수정이 끝났다면
	
	```shell
	git add .
	git commit -m "커밋 내용"
	git push origin master
	```
	
  + 를 사용해 배포 후 ` http://username.github.io ` 에서 본인의 첫 블로그를 확인 할 수 있다

<br>
+ 만약 사용하고 싶은 템플릿이 있다면
		
  + 해당 파일의 zip 파일을 가져와 압축을 해제 한 후 전부 나의 리포지토리에 붙여넣고 서버에 push를 진행하면 기본 템플릿이 제공된 테마가 제공된다

  + `jekyll` 테마들은 여러 웹사이트들에서 찾을 수 있으며 나는 ` http://jekyllthemes.org ` 에서 `yat` 이라는 이쁜 테마를 찾아 사용했다



이후 완성된 나의 첫 블로그 홈페이지

<img width="1173" alt="image" src="https://user-images.githubusercontent.com/66371206/149092791-01744bdf-8432-4e97-9f1c-522fcac07fe2.png">

그림은 나만의 작은 블로그를 위해 직접 그려 배너로 대체했다 ㅎㅎ