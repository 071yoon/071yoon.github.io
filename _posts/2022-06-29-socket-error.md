---
layout: post
title: Socket.io 연결 에러
subtitle: socket.io 연결 에러가 났을 경우
categories: TIL
tags: [TIL, Javascript]
---

## Socket 통신시 에러나는 경우

최근에 WebRTC를 사용하며 소켓 통신을 해야할 일이 생겼는데, 웬걸 자꾸 연결에서 실패를 하였다.

우선 결론은 dependency 확인이다. Front에서 연결을 하려는 소켓 통신인 경우 `socket.io-client`라는 module을 사용하는데, 이게 Back에 기다리고 있는 `socket.io` 모듈에서의 Server와 Socket의 dependency를 맞춰야 된다. 내가 Front 에서 사용하던 모듈은 ^4.3 이였고 백에서는 ^2에서 주었기 때문에 당연히 오류가 났다... 결국 둘 다 최근 모듈인 4세대로 바꾼 후 잘 작동하였다 ㅎㅎ
<img width="305" alt="image" src="https://user-images.githubusercontent.com/66371206/176819682-9a5fe411-e64c-4152-88dd-0c7c6d303155.png">

백엔드와 소통을 열심히 하자~

### 공식 document와 StackOverflow를 보며 했던 tries

```javascript
import { io } from "socket.io-client";
import * as io from "socket.io-client";
import io from "socket.io-client";
```

등 버전에 따라 import 하는 방식도 다른 것 같았다.

### 유의사항

> Backend에서 줄 때는 CORS에러에 유의하자.

> ES6의 React StrictMode를 꺼야지 Socket통신을 한번만 받는다. 켜두면 두번 받아서 한번은 잘 받고, 한번은 제대로 못받아서 오류가 났었다.
