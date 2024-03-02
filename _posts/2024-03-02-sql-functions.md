---
layout: post
title: SQL 함수 알아보기
subtitle: 잘 알지 못했던 SQL 함수들 공부해보기
categories: sql
tags: [sql]
---

## SQL 함수 알아보기

평소에 SELECT, INSERT, ALTER, CREATE 등 일반적인 DML, DDL문법만 사용하였는데 일을 하면서 다양한 문법들을 마주하고 공부하고 필요함이 느껴져 조금이나마 정리를 해보자고 한다

## COALESCE

> NULl이 아닌 첫번째 값을 반환하는 함수

행을 보고 COALESCE(col1, col2, col3) 이라면, 차례대로 col1, col2, col3 값을 보며 제일 먼저 null이 아닌 값을 반환하게 된다.

마지막 값에 static한 값을 넣어두면, NVL과 비슷한 형태로 사용할 수 있다.

## ROLLUP

> 합계를 쉽게 사용하기 위해 사용

- GROUP BY ROLLUP
- GROUP BY GROUP_COLUMN WITH ROLLUP

두가지 형태로 사용 가능하며, `GROUP BY` 를 하여 합계에 대한 새로운 row를 생성해준다.

## DECODE

ORACLE에서 사용하는 CASE WHEN 문법

`DECODE(GENDER, 'M', 'Male', 'F', 'Female', 'others')`

라 하면, GENDER가 'M' 이면 'Male', 'F'면 'Female', 둘 다 아니면 'others'인 문법

## CHARACTER / VARCHAR

CHAR인 경우 크기가 고정되며, 만약 부족하다면 우측에 공백을 넣어 사이즈를 무조건 맞춘다.

VARCHAR인 경우, 가변 길이기에 공백을 넣는 방식이 아님.

## CONNECT BY

> CONNECT BY, LEVEL, PRIOR 등 다양한 문법 세트
> https://vaert.tistory.com/166 이사람의 블로그를 보면 쉽게 이해가 가능하다

헷갈리는걸 정리 한번만 하자면 PRIOR 순서.

`connect by 직속상사 = prior 직원` 이라면 `방금 전 행의 직원 값이 현재 행의 직속상사 값인 모든 행을 찾아라`
즉 이 순서라면 직속상사가 상위 계층(LEVEL 1) 으로 뻗어져가는 노드의 형태가 되고,

`connect by prior 직속상사 = 직원` 이라면 `방금 전 행의 직속상사 값이 현재 행의 직원 값인 모든 행을 찾아라`
이 순서라면 제일 아래 직급이 상위의 계층이 되는 형태로 정렬이 된다.

## RANK

RANK는 `RANK() OVER()` 형태로 사용되며, 순위를 지정하는데 사용된다.
RANK 자체가 순위를 지정하기에, OVER 구문에 ORDER BY 로 순서를 지정해주어야 한다.

> ex. RANK() OVER(ORDER BY SOMETHING DESC) as rank

일반 RANK는 중복값이 들어갔을 경우, (1, 1, 3, 4, 5..) 이런 방향이라면 DENSE_RANK는 (1, 1, 2, 3, 4) 이렇게 중복과 순위의 측정 방식에 따라 다르다.

공부하다 새로운것이 생기면 더 정리할 것
