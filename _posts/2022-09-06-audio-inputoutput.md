---
layout: post
title: 브라우저에서 입출력 제어하기
subtitle: 브라우저 한 탭에서, 오디오 입출력을 제어해보자
categories: modoco
tags: [javascript, web]
---

## 브라우저에서 입출력 제어

현재 만들고 있는 프로제긑는 디스코드와 게더타운과 유사한, 실시간 통신이 들어간 웹 플랫폼이다. 이런 유사한 프로젝트에 필수적으로 들어가야 되는 요소는 오디오의 제어이다.

> 말을 하다가 마이크를 바꿔야 되는 상황이 된다면?

> 음질이 좋지 않다고 해서, 이어폰을 꼈을 때, 소리를 스피커에서 이어폰을 바꿔야 한다면?

> 이어폰 배터리가 나가서 중간에 스피커로 교체 해야된다면?

등 등 정말 많은 상황들이 연출 될 수 있다. 물론 가장 간편한 방법은, 알아서 시스템의 Default장비를 교체 후 브라우저를 refresh 해버리면 된다.
하지만 UX 관점에서, 오디오의 입출력을 브라우저에서 하는건 중요하다. 사실, 웹 플랫폼이 아닌 electron처럼 앱으로 만들었다면 쉬웠겠지만...

그렇게 브라우저 오디오 입출력 제어에 대한 글을 찾아보던 중 정말 쉽게 정리된 글도 없고, 오래된 내용들 특히 이미 지원이 끝난 함수들이 많아 막 사용하기 정말 쉽지 않았다. 그래서 내가 한번 정리를 해보았다.

#### 제공할 기능 목록

1. 카메라 입력 디바이스 제어가능
2. 음성 입력 디바이스 제어가능
3. 음성 출력 디바이스 제어가능

드랍다운 까지 완성된 형태는 [참고](https://velog.io/@071yoon/%EC%BB%A4%EC%8A%A4%ED%85%80-%EB%93%9C%EB%9E%8D%EB%8B%A4%EC%9A%B4-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98%EC%9D%84-%EB%A7%8C%EB%93%A4%EC%96%B4%EB%B3%B4%EC%9E%90)링크에 있다.

### 입출력 디바이스 찾기

우선, 선택을 하기 위해서는 나의 입출력 디바이스를 찾는것 부터 해야된다.

모든 선택 및 제어는 드랍다운에서 렌더링에도 사용되므로 전부 상태로 관리해주었다.

```typescript
// Selectors.tsx
export default function Selectors() {
  const [audioInput, setAudioInput] = useState<MediaDeviceInfo[] | null>([]);
  const [audioOutput, setAudioOutput] = useState<MediaDeviceInfo[] | null>([]);
  const [videoInput, setVideoInput] = useState<MediaDeviceInfo[] | null>([]);
  useEffect(() => {
    getDevice(setAudioInput, setAudioOutput, setVideoInput);
  }, []);

  return (
    <Container>
      <SelectorContainer>
        <Selector select="camera" device={videoInput} />
        <Selector select="mic" device={audioInput} />
        <Selector select="speaker" device={audioOutput} />
      </SelectorContainer>
    </Container>
  );
}
```

그리고 getDevice함수에서 현재 사용하고 있는 모든 장비들을 불러올 수 있는 `navigator.mediaDevices.enumerateDevices()`함수를 사용하였다.

`console`을 찍어보면 나온 device의 리스트는 다음과 같다.
<img width="985" alt="image" src="https://user-images.githubusercontent.com/66371206/188820065-c76d2d54-9e2a-4031-867f-1c3853347934.png">

여기서 `kind`로 분류하여 비디오 입력, 오디오 입력, 오디오 출력을 구별하여 따로 저장하였다.

```typescript
// getDevice.ts
const getDevice = async (
  setAudioInput: Dispatch<SetStateAction<MediaDeviceInfo[]>>,
  setAudioOutput: Dispatch<SetStateAction<MediaDeviceInfo[]>>,
  setVideoInput: Dispatch<SetStateAction<MediaDeviceInfo[]>>
) => {
  try {
    const devices = await navigator.mediaDevices.enumerateDevices();
    setAudioInput(devices.filter((device) => device.kind === "audioinput"));
    setAudioOutput(devices.filter((device) => device.kind === "audiooutput"));
    setVideoInput(devices.filter((device) => device.kind === "videoinput"));
    return devices;
  } catch (error) {
    console.log(error, "error getting devices");
    return "error";
  }
};
```

이렇게 주르륵 나온 devices들을 가지고 저번 포스트에서 다뤘던 드랍다운을 통하여 유저가 선택한 디바이스 정보를 나의 상태에 저장될 수 있게 설정해주었다.

> [저번 블로그 주소](https://velog.io/@071yoon/%EC%BB%A4%EC%8A%A4%ED%85%80-%EB%93%9C%EB%9E%8D%EB%8B%A4%EC%9A%B4-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98%EC%9D%84-%EB%A7%8C%EB%93%A4%EC%96%B4%EB%B3%B4%EC%9E%90)

### 출력 디바이스 제어

무사히 유저가 원하는 입출력 디바이스를 선택하였고, 그 정보가 `userAudioOutputDevice`라는 상태에 존재한다고 가정하고 진행하도록 하겠다.

사실 여기까지 왔다면 거의 다한것이나 마찬가지다. 적어도 출력은 정말 간단하다.

현재 우리의 플랫폼에서 지원하는 오디오 출력은 두가지 존재한다.

1. 테마 bgm
2. 상대방의 마이크 음성

우선 상대방의 마이크 음성을 제어하는 부분을 보면 다음과 같다.

```typescript
// Screen.tsx
useEffect(() => {
  if (videoRef.current && stream) {
    videoRef.current.srcObject = stream;
  }
  const newRef = videoRef.current;
  (newRef as any).setSinkId(userAudioOutputDevice?.deviceId);
}, [stream, videoRef, userAudioOutputDevice]);
```

테마로 나오는 음성을 다음과 같다.

```typescript
// Theme.tsx
useEffect(() => {
  if (volumeRef.current) {
    volumeRef.current.volume = volume;
  }
  const newAudio = volumeRef.current;
  (newAudio as any).setSinkId(userAudioOutputDevice?.deviceId);
}, [volume, userAudioOutputDevice]);
```

여기서 새로 추가된 부분은 setSinkId를 해주는 부분이다. 바로 deviceId를 넣어주면, 해당하는 device로 출력이 바뀌는 것이다. userAudioOutputDevice가 상태이므로, 해당 상태가 바뀔때 마다 sink를 맞춰서 video의 오디오를 변경 할 수 있게 useEffect로 감싸주고, dependency로 넣어주었다.

type을 any로 변경해준 이유는, 컴파일 할 때 setSinkId를 찾지못해 에러를 내기 때문이다. 추후에 Interface를 하나 만들어 setSinkId를 추가하여 방지할 예정이다.

여기서 문제는 호환성 문제이다.
<img width="823" alt="image" src="https://user-images.githubusercontent.com/66371206/188821917-ecf262ae-c9d0-449f-a9d6-e3675c879224.png">
보시다시피, Chrome, IE, Opera만 공식적으로 지원하기에, 만약 Safari로 접속한다면 바로 백색화면이 뜨는것을 볼 수 있다. 이 경우에는 browser 감지를 우선하여, Safari에서는 해당 함수를 실행하지 않도록 방지해줘야된다.

### 입력 디바이스 설정

입력디바이스는 생각보다 까다롭다.

우선, 전송하는 mediaTrack이 이미 getUserMedia에서 audio를 default로 가져왔을테니, 여기서 내가 마음대로 media 장비를 바꾼다고 해서 바로 바뀌지 않을 것이다. 그래서 생각해낸 방법은 다음과 같다.

1. 새로운 오디오 트랙을 만들어낸다 (새로 바뀔 입력 device인 트랙)
2. 현재 송출하고 있는 오디오 트랙을 삭제한다
3. 내 stream에 새로운 트랙을 추가한다
4. 보내고 있는 stream에 audio를 찾아 새로운 track으로 교체한다

```typescript
// useCreateMediaStream.tsx
const replaceAudioStream = async () => {
  try {
    const audioStream = await navigator.mediaDevices.getUserMedia({
      audio: {
        deviceId: userAudioInputDevice?.deviceId,
      },
    });
    myStream.localStream.removeTrack(myStream.localStream.getAudioTracks()[0]);
    myStream.localStream.addTrack(audioStream.getAudioTracks()[0]);
    Object.keys(pcs).forEach((pc) => {
      const sender = pcs[pc].getSenders().find((s) => s.track.kind === "audio");
      sender.replaceTrack(audioStream.getAudioTracks()[0]);
    });
  } catch (error) {
    console.log("failed to change audio stream", error);
    const stream = new MediaStream();
    setUserMediaStream(stream);
  }
};
```

그리고 이런 과정을 useEffect로 감싸서, userAudioInput이 바뀔때 마다 선언해준다.

```typescript
// settingModal
useEffect(() => {
  replaceAudioStream();
}, [replaceAudioStream, userAudioInputDevice]);
```

무사히 마치고 나면 입력 버튼에서, 이어폰 -> 맥북으로 바꾸어도 음성 입력트랙이 잘 바뀌는것을 확인 할 수 있다.

![ezgif-2-3f5a74b33f](https://user-images.githubusercontent.com/66371206/188824872-87f9ddaf-9bcc-44fc-955d-f7abe2beb227.gif)
