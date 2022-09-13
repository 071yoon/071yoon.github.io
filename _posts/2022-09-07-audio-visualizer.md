---
layout: post
title: 오디오 비주얼라이저 만들기
subtitle: 리액트로 오디오 비주얼라이저 만들기
categories: modoco
tags: [javascript, web]
---

## 개요

현재 진행중인 웹 플랫폼에서는 실시간 통신, 즉 음성 및 비디오가 필수적으로 들어가며, 오디오가 잘 나오는지 확인할 수 있는 오디오 비주얼라이저 또한 필수적으로 들어가게 되었다.

처음에 이걸 구현하기에는 다소 어려움이 있었는데, 우선 reference도 많이 없어 힘들었고, 막상 찾았더니 outdated 버전도 많이 있었다.

> 결국 mdn 문서를 뜯어가며 익혔다..

## 나만의 오디오 비주얼라이저 만들기

우선 개발하기 앞서, 디자인 시안이 나온 모습은 다음과 같았다.

<img width="593" alt="스크린샷 2022-09-13 오후 3 14 00" src="https://user-images.githubusercontent.com/66371206/189823880-d641cc67-7171-4775-a4bc-d7f880a562d3.png">

마이크를 확인하며, 볼륨이 높을수록 초록색이 올라가는 모습... 직관적이고 보기 좋다!

### audioContext

우선 오디오 비주얼라이저를 구현하기 위해서는 audioContext라는 객체에 대한 이해가 필요하다. 쉽게 말하자면, 오디오 볼률 및 frequency같은 것을 조절해주는 분석기라 볼 수 있다. 나의 audioContext generator는 다음과 같다.

```typescript
const audioContext = (stream: MediaStream) => {
  const audioContext = new AudioContext();
  const analyser = audioContext.createAnalyser();
  const microphone = audioContext.createMediaStreamSource(stream);
  microphone.connect(analyser);
  analyser.fftSize = 256; // 256 ~ 2048
  const bufferLength = analyser.frequencyBinCount;
  const dataArray = new Uint8Array(bufferLength);
  return { analyser, bufferLength, dataArray };
};
export default audioContext;
```

여기서 인자로 받는 stream은 내가 분석할 오디오 stream이다.
순서를 보면

1. 새로운 `audioContext`를 만든다
2. 해당 `audioContext`의 `analyser`(분석기)를 만든다
3. 나의 stream을 source로 하는 `audioContext`, 이하 `microphone`을 생성한다
4. 해당하는 `microphone`과 미리 만둘어둔 `analyser`를 연동시킨다.
5. `analyser`의 `fftSize`를 제어한다.
6. 나의 `analyser`에서 `bufferLength를` 구한다.
7. `bufferLength에` 해당하는 빈 양수배열의 `dataArray를` 생성한다
8. 필요한 요소들을 리턴해준다

여기서 생소한 요소들이 나올 수 있는데, fftSize는 mdn 문서상의 설명으로 `fftSize 속성은 unsigned long 값이고 주파수 영역 데이터를 얻기 위해 고속 푸리에 변환(FFT)을 수행할 때 사용될 샘플에서의 window 사이즈` 라고 한다. 즉 내가 자세한 정보를 가지고 싶다면 높일 수 있고, 자세하지 않은 정보를 원한다면 작게 설정이 가능하다는 뜻이다.

예제를 들어 더 자세하게 설명해보면, 주로 오디오 비주얼라이저의 예시는 두개로 볼 수 있다.

`block` 형태의 audio visualizer
<img width="307" height="200" alt="스크린샷 2022-09-13 오후 3 24 24" src="https://user-images.githubusercontent.com/66371206/189825782-d7832949-5654-4f87-9201-a5df168ec634.png" >

`주파수` 형태의 audio visualizer
<img width="307" height="200" alt="스크린샷 2022-09-13 오후 3 24 52" src="https://user-images.githubusercontent.com/66371206/189825786-cfbde43f-7883-4cb6-9ba4-7fb5bc9f6cbd.png" >

여기서 과연 어떤것이 더 정확한 수치 및 촘촘한 결과가 필요할까? 당연히 아래에 있는 주파수 대역일것이다. 블럭형태는 크게 잘라봐야 30 구간으로 잘려있지만, 주파수는 적어도 300 단위로 자른 후 선을 이어야 되기 때문이다. 이런 경우에서, block형태인 경우 fftSize를 조금 줄여도 되고, 주파수 형태인 경우 fftSize 즉 높은 sampleRate를 가지는것이 용이하다.

### get Frequency

이제 앞서 만든 audioContext를 가지고, 실제 volume 즉 frequency를 구해보도록 하자.

우선 `analyser`, `bufferLength`, `dataArray` 를 잘 가져오고, 가져온 `analyser`롤 통하여 `getByteFrequencyData()`함수를 실행하고, 인자로 `dataArray`를 넣는다.
그렇게 되면, 해당하는 `analyser`의 오디오 stream에서 호출된 시점의 frequency data를 앞서 만든 dataArray에 삽입해준다.
그렇게 완성된 data를 가지고, bufferLength만큼 loop을 돌며 평균을 내어주면, 호출한 시점의 audio volume값이라 볼 수 있다.

해당 코드는 아래와 같다.

```typescript
const { analyser, bufferLength, dataArray } = audioContext(userMediaStream);
analyser.getByteFrequencyData(dataArray);
const vol = audioFrequency(dataArray, bufferLength);
```

```typescript
// audioFrequency.ts
const audioFrequency = (dataArray, bufferLength) => {
  let total = 0;
  for (let i = 0; i < bufferLength; i += 1) {
    total += dataArray[i];
  }
  return total / bufferLength;
};

export default audioFrequency;
```

그럼 이제 getByteFrequencyData를 호출할 때 마다, 그에 해당하는 볼륨값을 찾을 수 있다.

나는 이 값을 setting이라는 모달창이 켜졌을 때만 동작하며, 30ms에서 한번씩 동작하면 되므로, 호출하는 완성된 코드는 다음과 같다. 그리고 이러한 volume을 state로 변경하여, 추후에 값이 바뀔 때 마다 렌더링 할 수 있게 설정하였다.

```typescript
useEffect(() => {
  let myInterval;
  const { analyser, bufferLength, dataArray } = audioContext(userMediaStream);
  if (setting) {
    myInterval = setInterval(() => {
      analyser.getByteFrequencyData(dataArray);
      const vol = audioFrequency(dataArray, bufferLength);
      setVol(Math.floor((vol / 256) * 100));
    }, 30);
  }
  return () => clearInterval(myInterval);
}, [setting]);
```

앞서 volume을 console에 찍어보면 다음과 같다.

![ezgif-3-ebf554f342](https://user-images.githubusercontent.com/66371206/189828981-909b12d2-1059-4952-a644-5a3d0c8e6f37.gif)

### Volume bar 구현

이제 거의 끝이 보인다. 해당하는 값을 int로 return 할 수 있게 완성하였으니, 해당하는 값을 volume에 맞게 초록색이 나오도록 설정만 하면 된다.

이 부분은 굳이 js를 사용할 필요도 없이, css로 넘겨주는 prop 즉 volume과 내가 몇번째 volume 아이콘인지 알아 낸후 몇 이상일 때, 초록색으로 표시를 해야 되는지 비교하며 관리할 수 있다.

```typescript
export default function VolumeBar({ volume }: { volume: number }) {
  const n = 8;
  return (
    <Container>
      <Bars>
        {[...Array(n)].map((no, index) => (
          <Bar key={Symbol(index).toString()} volume={volume} no={index} />
        ))}
      </Bars>
    </Container>
  );
}

const Bars = styled.div`
  width: 0.4rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  gap: 0.8rem;
`;

const Bar = styled.div<{ volume: number; no: number }>`
  width: 100%;
  height: 1.6rem;
  background-color: ${(props) =>
    props.volume / 8 > 7 - props.no ? "#4ADE80" : "rgba(255, 255, 255, 0.3)"};
  border-radius: 5rem;
`;
```

> map 의 index로 내가 아래에서 몇번째인 볼륨 바 인지 알아 낸 후, volume / 8 보다 클 경우 초록색, 아니면 회색으로 처리

### 시연

그렇게 해서 완성된 모습은 다음과 같다

![ezgif-3-7171c44a73](https://user-images.githubusercontent.com/66371206/189821154-647e4008-d940-407d-91b3-b03b7ce4d364.gif)
