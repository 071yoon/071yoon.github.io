---
layout: post
title: firebase 정보 백업하기 (export)
subtitle: firebase data를 무료로 백업해보자
categories: TIL
tags: [web]
---

## 배경

firebase를 활용해서 개인 프로젝트를 잘 하고 있었는데, 문득 백업이 하고싶어졌다.

google이 갑자기 내 data를 날려버리거나 하지는 않겠지만 내가 실수로 table을 날렸을 경우 복구도 되지 않을 것 같고 (가뜩이나 무료 버전을 쓰고 있기에) 점점 traffic이 올라감에 한번은 백업이 필요하다 생각이 들었다.

## Firebase 둘러보기

솔직히 firebase에 backup 혹은 export 기능이 없을리가 없다

<img width="1155" alt="screenshot-1" src="https://github-production-user-asset-6210df.s3.amazonaws.com/66371206/287666447-8eb0250d-43c4-4f8c-9cb2-c9248b69a0d1.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231204%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231204T102651Z&X-Amz-Expires=300&X-Amz-Signature=c39f1250399b5f90a767d999c3a8c6c22de1136ac5f4a4a8e8dbd0b30045af4a&X-Amz-SignedHeaders=host&actor_id=66371206&key_id=0&repo_id=713644713">

당연히 있다..!

하지만 아니나 다를까 결제 정보를 입력하고 돈을 내야 가능하는것 처럼 보인다.

<img width="542" alt="image" src="https://github-production-user-asset-6210df.s3.amazonaws.com/66371206/287667522-5c13b841-ee6c-41c9-a6d4-5d636ddaf6df.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231204%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231204T102845Z&X-Amz-Expires=300&X-Amz-Signature=aa125a5b8c6590a989a87921563790c0cece6ff6bffb55bc8c85b1d29f89bedb&X-Amz-SignedHeaders=host&actor_id=66371206&key_id=0&repo_id=713644713">

(사실 GCP와 연결하는것 같아 당연히 결제 정보가 필요한것이고 실제로 돈이 필요한지는 모르겠음.. 그냥 혹시나 카드정보 입력해 두었다가 firebase traffic이 폭주해서 지갑이 털릴까봐 조금 무서웠다)

그래서 다른 방법을 찾아 나서기로...!

## github 돌아다니기

솔직히 firebase는 문서화가 잘 되어 있기도 하고, query를 통해 data를 읽기/쓰기가 매우 쉬워 손쉽게 코드로 export 하는 기능을 찾는것도 어렵지 않다고 생각한다. 하지만,, 누군가 더 깔끔하게 만들어 두었다면..? 굳이 내가 만들 필요가 있을까? 라는 생각에 뒤적 뒤적,.. firebase export를 찾아보기 시작했다..

### node-firestore-import-export

기가 막힌 library를 찾아내고야 말았다. 바로 이름부터 직관 그 자체인 [node-firestore-import-export](https://github.com/jloosli/node-firestore-import-export)!

두가지 활용 방법이 존재한는데

1. npm 으로 전역으로 library를 설치 후 export하여 json 형태로 받기
2. 코드로 export/import 기능을 구현하여 web application 내부에서 활용

나는 굳이 export나 import를 web으로 할것이 아니기에 1번 방법으로 수동으로 백업을 하였다.

워낙 README가 잘 되어 있어 직접 레포에 확인해보는것이 더 정확하겠지만, 간략한 사용법은 다음과 같다.

1. firebase에서 비공개 키 생성

   > 프로젝트 설정 -> 새 비공개 키 생성
   > <img width="652" alt="image" src="https://github-production-user-asset-6210df.s3.amazonaws.com/66371206/287669364-17a64c41-87ff-460a-a6de-8e03d5df6d98.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231204%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231204T103523Z&X-Amz-Expires=300&X-Amz-Signature=0b129137aa603c9278b4d41b70a55b0860a05aa1f43bb1e902dfc38b5f1a202c&X-Amz-SignedHeaders=host&actor_id=66371206&key_id=0&repo_id=713644713"> > <img width="1031" alt="스크린샷 2023-12-04 오후 7 36 31" src="https://github-production-user-asset-6210df.s3.amazonaws.com/66371206/287669853-8b563173-aafa-4d65-b92e-797ae5722c1e.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231204%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231204T103707Z&X-Amz-Expires=300&X-Amz-Signature=e1f7a0538f30ccae00c56c933e00c2ff9043822f4980c9d036d4ec5d094722e8&X-Amz-SignedHeaders=host&actor_id=66371206&key_id=0&repo_id=713644713">

2. npm package 설치 및 export

```shell
# sudo로 해야될 수 있음
npm install -g node-firestore-import-export

# ..비공개 키 저장된 위치로 이동..
firestore-export --accountCredentials path/to/credentials/file.json --backupFile /backups/myDatabase.json
```

그리고 이러한 open-source library를 사용하는 경우 내 credential 이 외부로 노출이 되지는 않을까.. 하는 생각으로 항상 검증 해봐야 된다.

실제로 export 하는 부분을 링크로 남겨둘테니 한번씩은 확인해 보았으면 한다. (내가 봤을때는 문제 없음)

> [firebase-export 링크](https://github.com/jloosli/node-firestore-import-export/blob/master/src/lib/export.ts)

아주 깔끔~ 하게 json 으로 export가 된다.

<img width="482" alt="스크린샷 2023-12-04 오후 7 41 45" src="https://github-production-user-asset-6210df.s3.amazonaws.com/66371206/287671568-b25dbdb7-89ef-4d97-ad42-03acc4bd0314.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231204%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231204T104311Z&X-Amz-Expires=300&X-Amz-Signature=ec01c6fed067e400411dcbc260102539c75099679d9bd419c876bddf05de9d32&X-Amz-SignedHeaders=host&actor_id=66371206&key_id=0&repo_id=713644713">

다음엔 아마 oracle 무료 cloud를 받은 기념,, 하루에 한번씩 backup 하도록 자동화 해둘 예정..

총총..
