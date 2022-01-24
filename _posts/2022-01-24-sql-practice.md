---
layout: post
title: SQL 예제를 풀어보자
subtitle: SQL 고득점 마스터하기
categories: sql
tags: [sql, qeury]
---

## Why SQL?

---

`SQL`은 내가 데이터베이스 강의를 들을 때 잠깐 만져보고 처음 본 `Query` 언어이다. 그 당시에 효율이 정몰 좋고 데이터베이스 관리를 위해서 꼭 사용해야 하는 언어라고 배웠고, 실제로 `MariaDB` 를 활용한 웹을 만들어 보면서 왜 사용해야되는지 배워보았다. 그리고 `SQl` 을 펼쳐보지도 않은지 거의 두 세달 이 되어서, S/W Maestro 관련 글을 보던 중 코딩테스트에 `SQL`이 나온다는 소식을 들었다... ㅜㅜ 그래서 어쩔 수 없이 `SQL` 문제들을 살펴보던 중 프로그래머스에 `SQL` 문제들이 올라와 있는걸 보았고, 기본기를 다지는데 훌륭하다고 하여서 한번 풀어보기로 했다.

> 그리고 어차피 한번 리마인드 할 때 됐다!!

문제풀기를 진행하며 까다로웠던 부분들을 다시 보자

## SELECT

---

`SELECT`는 `SQL`을 처음 배우면 나오는 문법이다. 그냥 찾아주는거고 여기서 뭐 어려운 것은 없다.

```sql
SELECT ANIMAL_ID
FROM ANIMAL_INS
WHERE ANIMAL_ID > 100
ORDER BY DATETIME ASC #DESC
LMIIT 1
```

이런식으로, 찾는다 -> 어디서? -> 어떻게? 이런식으로 주면 된다

## COUNT

---

`COUNT(*)` 로 무엇의 개수를 셀 지 정할 수 있다.
중요한점은 `GROUP BY` 로 어떤것의 개수를 어떤 그룹으로 쪼개서 계산할 지 할 수 있고, 같은것을 세기가 싫다면 `COUNT(DISTINCT NAME)` 이런식으로 할 수 도 있다.

```sql
SELECT HOUR(DATETIME) as HOUR, COUNT(DATETIME) as COUNT
FROM ANIMAL_OUTS
GROUP BY HOUR(DATETIME) HAVING HOUR >= 9 AND HOUR <= 19
ORDER BY HOUR(DATETIME)
```

09:00부터 19:59까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문

## IS NULL

---

`NULL` 문은 특징이 있다. 우선 단독으로 `Something = 'NULL'` 이런식으로 사용 할 수 없고, `Something is NULL` 이런식으로 사용해야 알아듣는다. 생각해보면 당연한 것

그리고 만약 `NULL` 이면 이러한 구문을 넣어라 이런것도 가능하다

```sql
SELECT ANIMAL_TYPE, IFNULL(NAME, "No name") as NAME, SEX_UPON_INTAKE
FROM ANIMAL_INS
```

이렇게 하면 만약 `Name` 에 `NULL` 이라면 `No name` 이 들어가게 된다.

## JOIN

---

`SQL`의 꽃이라고 할 수 있는 `JOIN` 문이다. 간단하게 말하면 두 테이블을 합쳐주는 것인데, 합치는 과정에서 어떤식으로 문제를 찾을 지 정할 수 있는 방법이 무궁무진하기 떄문에, 만약 제대로 알고 가지 못한다면 어려워 질 수 있다. 실제로 내가 공부를 할 때는 `Natural JOIN` 으로 다 풀었었는데, 문제에서는 다 특이한 방법으로 값들을 찾으라 해서 조금 까다로웠다.

`OUTS` 에는 있지만 `INS` 에는 없는 데이터 출력

```sql
SELECT
    OUTS.ANIMAL_ID, OUTS.NAME
FROM
    ANIMAL_INS AS INS
RIGHT JOIN
    ANIMAL_OUTS AS OUTS
ON
    INS.ANIMAL_ID = OUTS.ANIMAL_ID
WHERE
    INS.ANIMAL_ID IS NULL;
```

보호소에서 중성화 즉 Intact -> Neutered or Sapyed가 된 경우 찾기

```sql
SELECT INS.ANIMAL_ID, INS.ANIMAL_TYPE, INS.NAME
FROM ANIMAL_INS INS
INNER JOIN ANIMAL_OUTS OUTS
ON INS.ANIMAL_ID = OUTS.ANIMAL_ID
WHERE INS.SEX_UPON_INTAKE LIKE 'Intact%' AND (OUTS.SEX_UPON_OUTCOME LIKE 'Neutered%' OR OUTS.SEX_UPON_OUTCOME LIKE 'Spayed%')
ORDER BY ANIMAL_ID
```

## STRING, DATE

---

`CASE` 를 나눠서 만약 1번이면 `X` 2번이면 `O` 를 출력하는 테이블을 만들 수 있다

```sql
SELECT ANIMAL_ID, NAME,
CASE WHEN SEX_UPON_INTAKE LIKE 'Neutered%' OR SEX_UPON_INTAKE LIKE 'Spayed%' THEN 'O' ELSE 'X' END AS '중성화'
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

`DATE` 를 뺼 때, 두가지 경우가 있는데 `DATEDIFF` 함수를 사용해서, 실제로 차이가 얼마나 나는지 원하는 데이터 타입으로 찾을 수 있고 아니면 그냥 변수마냥 빼도 알아서 게산을 해준다

```sql
SELECT INS.ANIMAL_ID, INS.NAME
FROM ANIMAL_INS INS
INNER JOIN ANIMAL_OUTS OUTS
ON INS.ANIMAL_ID = OUTS.ANIMAL_ID
ORDER BY OUTS.DATETIME - INS.DATETIME DESC
LIMIT 2
```

`DATE` 에는 내장 함수들이 많은데, 그 중에 `DATE_FORMAT` 을 통해 내가 원하는 형식을 지정 해 줄 수 도 있다.

```sql
SELECT ANIMAL_ID, NAME, DATE_FORMAT(DATETIME, '%Y-%m-%d') as '날짜'
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

이렇게 하면 내가 원하는 YEAR-MONTH-DAY 형식으로 출력이 된다.

> {Y,y} {M,m} {D,d} 모두 형식이 다르다. [문자열 vs 숫자]
