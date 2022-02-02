---
layout: post
title: 리액트JS로 앱 만들기 1
subtitle: 리액트가 무엇이고 어떻게 사용하는지 알아보자
categories: react
tags: [react, javascript, TIL, web]
---

## React란 무엇인가

React는 간단하게 말해서, 웹의 `Framework`이다.

> 정확하게는 사용자 인터페이스를 만들기 위한 Javascript Library

그리고 `React` 란 놈에게는 큰 장점이 있다.

1. 선언형
2. 컴포넌트 기반

이렇게 볼 수 있다.

### 선언형

---

선언형은 React는 상호작용이 많은 UI를 만들 때 생기는 어려움을 줄여준다. 즉 애플리케이션의 각 상태에 대한 간단한 뷰만 설계를 한다면 알아서 React가 데이터가 변경됨에 따라 적절한 컴포넌트만 효율적으로 갱신하고 렌더링을 해준다. 즉 말 그대로 `React` 하게 해준다는 것이다.

간단하게 보면, `React` 파일에서

```javascript
const btn = React.createElement(
  "button",
  {
    onClick: () => console.log("im clicked"),
  },
  "Click me"
);
```

이런식으로 HTML 으로 선언하지 않고도 단번에 생성과 동시에 옵션을 설정 할 수 있다.

### 컴포넌트 기반

---

스스로 상태를 관리하는 캡슐화된 컴포넌트를 만들 수 있다.

여기서 상태를 관리한다는 말이 리액트에서는 꽤나 중요한 포인트라 할 수 있다. 우선 리액트 컴포넌트는 `render()` 함수를 이용하여, 데이터를 입력받아 화면에 표시할 내용을 리턴을 한다는 점에서 자바스크립트와 다르다. 그리고 이러한 컴포넌트는 `this.props`를 이용하여 입력 데이터를 다루고, `this.state`로 접근 할 수 있다. 그리고 상태 데이터가 바뀌면 `render()` 가 다시 호출되어 갱신이 되는 구조이다.

말로 들어서는 이해가 잘 되지 않고, 역시 예제를 보며 학습을 하는것이 필요할 것 같다.

또하나의 큰 장점을 `React Native` 로의 방향이 자유롭다는 것이다. `Native` 언어란, iOS app 에서 Swift 처럼 그 자체의 OS 를 활용하여 만드는 앱인데, 물론 제작을 하여도 진짜 Native 급으로 속도가 나오지는 않는다. 하지만, 그와 비슷한 속도를 낼 수 있고 문법 자체가 너무나도 비슷해서, 금방 금방 수정하여 웹뿐만이 아닌 페이스북 혹은 인스타그램 처럼 웹앱으로 변환이 가능하다.

### JSX 의 활용

JSX 가 나옴으로써, `React` 가 단순히 Javascript 처럼 활용하는것이 아니라, 마치 `HTML` 에서 사용한 것 처럼도 사용 할 수 있게 되었다. 이것또한 예제를 보고 확인해보자

```jsx
const Title = (
  <h3 id="title" onMouseEnter={() => console.log("mouse enter")}>
    Hello I am a title
  </h3>
);
```

이런식으로 `CreateElement` 를 사용하는것이 아니라, 마치 `HTML` 에서 태그를 해서 만드는것인데, 동시에 자바스크립트를 활용하여 만들 수 있게 되었다. 하지만 코드를 잘 보면 `CreateElement` 를 아예 안쓰는 것은 아니고, 이런식으로 `HTML` 처럼 편하게 적어두면 `BABEL` 이란 컴파일러에서 내부적으로 `CreateElement` 형으로 변환을 하여 적어주는 것이다.

> 여기서 중요한점은 함수나 변수명을 할 때 대문자로 시작을 해야 실제 HTML 의 태그 `button` 같은것과 헷갈리지 않는다

### State 란

기본적으로 데이터가 저장되는 곳이라고 보면 쉽다.

간단한 클릭형 버튼을 만들어서 확인해보자

```jsx
<script type = "text/babel">
	const root = document.getElementById("root");
	let counter = 0;
	function countUp(){
		counter += 1;
	}
	function Container() {
		return (
			<div>
				<h3> Total Clicks: {counter}</h3>
				<button onClick={countUp}>Click me</button>
			</div>
		);
	}
	ReactDom.render(<Container />, root);
</script>
```

이렇게만 보면 좋은 코드같아 보인다. 하지만 여기는 맹점이 있는데, 바로 Rendering 을 한번밖에 하지 않아, 아무리 클릭을 눌러도 웹상에서는 안보이는다는 것이다. 그래서 `State` 가 필요하게 된다.

가장 손쉬운 방법은 countUp 함수가 할 때마다, `Render` 를 다시 불러주게 되면 해결 할 수 있다. 하지만 아무리봐도, 효율적인 부분이 아니다. 왜냐면 전부 다 렌더링을 하는것이 비효율적이고, 계속 렌더링을 해줘야되는것을 함수를 만들면서 생각을 하면서 설계를 해줘야 되기 떄문이다. 그럼 `그 변수`만 업데이트를 하려면 어떻게 해야될까?? 그게 바로 `React` 의 최대 장점이다. 즉 매우 `Interactive` 하고 `Effective` 한 프로그램이 만들어 지는것이다.

```jsx
<script type = "text/babel">
	const root = document.getElementById("root");
	function Container() {
		let [counter, modifier] = React.useState(0);
		//counter = data[0], modifier = data[1]
		const onClick = () => {
			modifier(counter + 1);
			//OR modifier((current) => current + 1);
		};
		return (
			<div>
				<h3> Total Clicks: {counter}</h3>
				<button onClick={onClick}>Click me</button>
			</div>
		);
	}
	ReactDom.render(<Container />, root);
</script>
```

이렇게 설정하면, 클릭이 일어날 때 마다, modifier에서 counter + 1 로 해준 값이 반환되며, `modify` 가 일어날 때 마다, 딱 그 부분만 렌더링을 해주는 것이다.

### Props

Props 란 부모 컴포넌트로부터 자식 컴포넌트로에게 데이터를 보낼 수 있게 해주는 방법이다.

> Ex) 버튼의 스타일을 하나하나 다 바꿔주지 말고, 어차피 공통으로 갈거면 그러한 템플릿을 저장해 두었다가 사용하면 더 편하지 않겠냐 라는 것..!!

```jsx
function Btn(props) {
  reutnr(
    <button
      styles={{
        backgroundColor: "tomato",
        color: "white",
        padding: "10px 20px",
        border: 0,
        borderRadius: 10,
      }}
    >
      {props.text}
    </button>
  );
}

function App() {
  return (
    <div>
      <Btn text="Save changes" />
      <Btn text="Continue" />
    </div>
  );
}
```

이런식으로 사용 할 수 있다.

### React Memo

위 예제를 보면 버튼이 `Save Changes` 와 `Continue` 두개가 생성되었다. 하지만 만약, `State`를 활용하여, 첫 버튼만 렌더를 해주려고 해도, 두 버튼 다 렌더링이되는 불상사가 발생한다. 이러한 사태를 어떻게 막을 수 있을까?? 리액트 메모를 활용하여 막을 수 있다.

> const MemorizedBtn = React.memo(Btn);

라고 활용하고나서, 그 후 Btn이 아니라 MemorizedBtn 으로 호출하면 렌더링을 한번만 해도 될 수 있게 된다!!
