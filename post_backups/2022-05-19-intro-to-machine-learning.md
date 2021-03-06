---
layout: post
title: Intro to Machine Learning
subtitle: 머신러닝을 배워보자
categories: ML
tags: [ML, Machine Learning, study]
---

## Definition of Machine Learning

### 우리는 어떻게 문제를 해결할 수 있을까??

- 컴퓨터 공학도로써, 문제를 해결하기위하여 일정한 규칙을 행동하는 프로그램을 작성한다
- 많은 상황에서, 이러한 규칙들을 정하는것이 매우 힘들다

### `Learning Systems`들은 문제를 해결하기 위함이 아니다

- 어떤식으로 동작해야되는지에 대한 프로그램을 개발
- 시행착오를 통하여 어떤식을 통하여 프로그램을 개발

| `Learning` 이란 단순히 학습된 예제들을 시스템에 도입하는것에 지나지 않는다.

### Arthur Samuel

- 머신러닝은 컴퓨터가 프로그램되지 않아도 배울수 있는 능력을 구축 할 수 있게 학습권을 주는것이다

### Tom Mitchell

- 머신러닝은 `Experience` 를 통하여 배우며, `Task` 를 반영하며, `Performance`에 신경쓰여야 된다. 그리고 이러한 특정한 `Task`에 대하여 `Performance` 로 측정되며, `Experience`를 통하여 발전한다.

- Experience : 프로그램이 게임을 하는 과정
- Performance measure : 우승률

## Tasks that requires ML

- 숫자를 보고 어떻게 2라고 판단을 할까?
- 2와 7의 차이를 어떻게 분석 할 수 있을까?
- 어떻게 로봇을 요리 할 수 있게 만들 수 있을까?

## Learning algorithms are useful in many tasks

- 머신러닝은 AI의 방법으로 자리잡고있다

- 컴퓨터의 새로운 능력이다

- 예시
  - Data mining
    - 자동 혹은 웹으로 배포된 대량의 데이터베이스
    - Web click data, medical records, biology ...
  - Application can't program by hand
    - NLP, Computer vision
    - 자율주행, 필기인식
  - Self-customizing programs
    - 아마존,넷플릭스 자동 추천
  - Understanding human learning
    - Brain, Real AI

## Taxonomy of Machine Learning

### Supervised Learning

#### Housing Price Prediction

- Supervised Learning : `정답`이 항상 정해진다
- Regression : 연속적인 값들을 통하여 예측을 할 수 있다

목표 : 값들이 듬성듬성 주어졌을 때, 선형분석을 하여 중간에 어떤 값을 주었을 때 예측을 할 수 있다.

#### Breast Cancer

- Classification : 1 혹은 0으로 정해진 값

Tumor Size, Malignant, Age, Clump thickness, Uniformity of cell size... 로 분석하여 유방암인지 아닌지 분석 할 수 있다.

#### Supervised Learning in Computer Vision

- Image Classification

  - X : raw pixels of image [이미지를 주면]
  - Y : main object [어떤 물체인지 판단해준다]

- Object localization and detection
  - X : raw pixels of image [이미지를 주면]
  - Y : bounding boxes [물체들에 박스를 쳐준다]

### Unsupervised Learning

- Dataset에 라벨이 없다
- 목표 : 데이터를 보고 특이한 지점을 발견하는것

#### Clustering

- K-mean Clustering 혹은 가우시안을 통해 찾아낸다

- 예를들어, 뉴스에서 헤드라인을 서로 엮이는것들만 뽑아서 묶는 과정

### Reinforcement Learning
