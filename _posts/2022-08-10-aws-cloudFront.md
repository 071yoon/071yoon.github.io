---
layout: post
title: S3 & CloudFront를 활용한 CDN 서버 만들기
subtitle: CloudFront CDN Server
categories: TIL
tags: [TIL, AWS]
---

<img width="300" alt="image" src="https://user-images.githubusercontent.com/66371206/183911780-90c424e9-e115-44e2-8163-f0b4ab823458.png">

CDN 서버를 만들기 앞서, 왜 CDN 서버를 만드는지에 대해 알아보자

## CDN

### CDN 이란?

콘텐츠 전송 네트워크(CDN)는 데이터 사용량이 많은 애플리케이션의 웹 페이지 로드 속도를 높이는 상호 연결된 서버 네트워크로 콘텐츠 전송 혹은 배포 용도로 사용된다.

실제 사용자는 웹사이트에 접속 시, 해당 웹사이트의 데이터를 다운받기 위해 인터넷으로 접속한다. 하지만, 사용자가 서버에서 떨어질 수록, 웹사이트의 정적 이미지(이미지, 동영상, 음악 등)를 다운받는데 오래 걸리게 됩니다. 이러한 경우, CDN 서버를 전 세계에 있는 엣지 포인트를 두어 캐싱처리를 하여 사용자가 가장 가까운 지역의 엣지 포인트로 접속하게 하여 해당 문제를 해결 할 수 있다.

## CDN이 중요한 이유?

콘텐츠 전송 네트워크(CDN)의 주 목적은 대기 시간을 줄이거나 네트워크 설계로 인해 발생하는 통신 지연을 줄이는 것이다. CDN은 클라이언트와 웹 사이트 서버 간에 중간 서버를 두어 효율성을 높일 수 있다. 이러한 CDN 서버는 클라이언트-서버 통신을 관리하며, 웹 서버에 대한 웹 트래픽을 줄이고, 대역폭 소비를 줄이며, 애플리케이션의 사용자 환경을 개선할 수 있다.

## CDN의 장점?

CDN을 사용함으로써 다음과 같은 이득을 볼 수 있다.

### 페이지 로드 시간 단축

페이지 로드 시간이 너무 느리면 웹 사이트 트래픽이 감소할 수 있으므로, 반송률을 줄이고 사용자가 사이트에서 보내는 시간을 늘릴 수 있다.

### 대역폭 비용 절감

들어오는 모든 웹 사이트 요청은 네트워크 대역폭을 사용하기 때문에 대역폭 비용이 상당히 높다. 캐싱 및 기타 최적화를 통해 CDN은 오리진 서버가 제공해야 하는 데이터의 양을 줄여 웹 사이트 소유자의 호스팅 비용을 절감할 수 있음.

### 콘텐츠 가용성 제고

한 번에 너무 많은 방문자가 방문하거나 네트워크 하드웨어 오류가 발생하면 웹 사이트가 중단될 수 있습니다. CDN 서비스는 더 많은 웹 트래픽을 처리하고 웹 서버의 로드를 줄일 수 있습니다. 또한 하나 이상의 CDN 서버가 오프라인으로 전환되면 다른 운영 서버가 해당 서버를 대체하여 서비스가 중단되지 않도록 할 수 있습니다.

### 웹 사이트 보안 강화

분산 서비스 거부(DDoS) 공격은 대량의 가짜 트래픽을 웹 사이트로 전송하여 애플리케이션이 작동 중지되도록 만들려고 시도합니다. CDN은 여러 중간 서버 간에 로드를 분산하여 오리진 서버에 미치는 영향을 줄임으로써 이러한 트래픽 급증을 처리할 수 있습니다.

## CDN 구현

### S3 버킷 생성

먼저 S3 버킷을 일반적으로 생성한다.
<img width="844" alt="image" src="https://user-images.githubusercontent.com/66371206/183887243-2b1dd102-166b-4e1f-bbc8-846ff990d27f.png">

> CloudFront에서 public으로 열어줄 예정이라, 액세스 차단을 해도 된다

<img width="835" alt="image" src="https://user-images.githubusercontent.com/66371206/183887348-9e248bf8-a7c3-4b79-857f-68a672208981.png">

다음으로, 프로젝트에서 사용할 static 파일들을 넣어준다.

<img width="1202" alt="image" src="https://user-images.githubusercontent.com/66371206/183887581-979abfb6-6557-416a-813c-a7cf04a79817.png">

마지막으로 버킷정책과 CORS 정책을 수립해주면 된다.
![image](https://user-images.githubusercontent.com/66371206/183889538-902d219b-75f3-48aa-a7b9-541a9a788125.png)

> 버킷정책

```json
{
  "Version": "2008-10-17",
  "Id": "PolicyForCloudFrontPrivateContent",
  "Statement": [
    {
      "Sid": "1",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity [my id]"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::static.[myDomain].com/*"
    },
    {
      "Sid": "2",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity [my id]"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::static.[myDomain].com/*"
    }
  ]
}
```

> CORS 정책

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["HEAD", "GET"],
    "AllowedOrigins": ["*"],
    "ExposeHeaders": [
      "ETag",
      "x-amz-server-side-encryption",
      "x-amz-request-id",
      "x-amz-id-2"
    ],
    "MaxAgeSeconds": 3000
  }
]
```

### CloudFront 연동

아까만든 S3 버킷으로 CloudFront를 생성해주면 된다
![image](https://user-images.githubusercontent.com/66371206/183890057-0a6c3ebb-9a0f-4886-8113-f6cc92ab5010.png)

여기서 나온 ID를 앞서 버킷 정책에서의 [my id]에 넣어주면 된다.

상세 정책은 다음과 같다

![image](https://user-images.githubusercontent.com/66371206/183895266-496ab645-577b-45a2-a003-b374bfcabe70.png)

![image](https://user-images.githubusercontent.com/66371206/183895316-83d7189c-5310-4d2e-931f-84dd6c21a565.png)

CloudFront를 public으로 열어두고, CORS 정책을 S3에 위임하면 된다.

## Domain 달기

Domain은 AWS 내 Route 53에서 미리 구매한 Domain을 확장해서 사용하였다.

![image](https://user-images.githubusercontent.com/66371206/183895726-b2486618-83c4-48a9-b269-9f4af7a8fb24.png)

![image](https://user-images.githubusercontent.com/66371206/183895901-8c996509-d9b8-461d-8ec4-f611672fbb01.png)

그리고 다시 CloudFront로 와서, 생성한 대체 도메인을 선택 후, https 를 사용하기에 해당 SSL 인증서를 등록해주면 static.domain.com 으로 접근 가능한 cdn 서버가 완료가 된다.

![image](https://user-images.githubusercontent.com/66371206/183910403-19ac163e-a202-4a59-8430-a81beae4385b.png)

참조
[https://aws.amazon.com/ko/what-is/cdn/](https://aws.amazon.com/ko/what-is/cdn/)
