---
layout: post
title: REACT Hook 구현하기
subtitle: REACT Hook을 직접 구현해보자
categories: TIL
tags: [TIL, JavaScript]
---

# React Hook 구현해보기

## 클로저

### 클로저의 개념

Hook 을 구현하기 앞서 Closure에 대한 개념을 알고 가야된다. 클로저는 자바스크립트 처음 등장한 것은 아니며 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어에서 사용되는 중요한 특성이다.

MDN 공식문서를 읽어 보면 다음과 같다.

> A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function's scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.

즉 함수와 선언 됐을때의 렉시컬 환경과의 조합이며, function 내부에서 function 바깥에서의 범위까지 접근할 수 있게 해준다는 뜻으로 해석된다.

### 클로저 예제

개념이 잘 와닿지 않을 수 있으므로 예제를 보고 이해해보자.

```javascript
function outerFunc() {
  var x = 10;
  var innerFunc = function () {
    console.log(x);
  };
  innerFunc();
}

outerFunc(); // 10
```

코드에서 보이듯 outerFunc에서 innerFunc가 선언되고, 호출도 되었다. 하지만 여기서 핵심은 `var x`이다. innerFunc에서 `x`가 호출되었지만, 이는 외부함수인 `outerFunc`에서 선언되어있다. 여기서 innerFunc가 함수 outerFunc의 내부에 선언된 내부함수이므로 innerFunc는 자신이 속한 렉시컬스코프를 참조 할 수 있으므로 x에 접근할 수 있다.

> scope는 함수를 호출할 때가 아닌 어디에 선언했는지에 따라 달라지며, 이를 렉시컬 스코핑이라 한다

실행 컨텍스트 관점에서 보면 다음과 같다.

1. 내부 innerFunc가 호출되며 자신의 실행 컨텍스트가 스택에 쌓이고 변수 객체, 스코프 체인, this에 바인딩할 객체가 결정
2. 스코프 체인은 전역 스코프를 가리키는 전역 객체, outerFunc의 활성객체, 함수 자신의 스코프를 가리키는 활성 객체를 바인딩 -> 렉시컬 스코프

앞선 예제를 해석하면 다음과 같다

1. innerFunc 내부에서 x를 검색 -> 실패
2. innerFunc의 외부함수 outerFunc의 스코프에서 x를 검색 -> 성공

이제 innerFunc를 호출이 아닌 return으로 변환하여 보자

```javascript
function outerFunc() {
  var x = 10;
  var innerFunc = function () {
    console.log(x);
  };
  return innerFunc;
}

var inner = outerFunc();
inner(); // 10
```

이제 outerFunc는 innerFunc를 반환하고 끝이 났다. 즉 1회 실행 후 실행 컨텍스트에서 제거되었으므로 outerFunc의 `var x`가 유효하지 않아, 접근할 수 있는 방법이 없어 보인다. 하지만 실제로 실행해보면 10이 잘 나온다. 이렇게 자신을 포함한 외부함수보다 내부함수가 더 오래 유지되는 경우, 내부함수가 호출되더라도 외부함수의 변수에 접근할 수 있는 환경을 클로저라 부른다.

> 반환된 내부함수가 자신이 선언됐을 때의 환경인 스코프를 기억하여 환경 밖에서 호출되어도 환경에 접근 할 수 있는 함수

> 클로저에 의해 참조되는 변수는 자유변수라고 부른다

## Hook 구현해보기

### useState Hook

먼저 useState의 사용 예시를 보면 다음과 같다.

```javascript
const [counter, setCounter] = useState(0);
console.log(counter); // 0
setCounter(1); // 1 and reflow
console.log(counter); // 1
```

즉 간단하게 생각하면, useState를 destruct한 형태로 받아오고, 첫번째 인자로 state에 대한 정보를 호출, 두번째 인자로 state를 변경할 수 있는 React의 common hook 이라 할 수 있다.

그럼 위와 유사하게 useState hook을 직접 구현해보자.

> state() 함수에서 나의 상태, setState(val)에서 상태 변경

```javascript
function useState(init) {
  var val = init;
  function state() {
    return val;
  }
  function setState(newVal) {
    val = newVal;
  }
  return [state, setState];
}
var [foo, setFoo] = useState(0);
console.log(foo()); // 0
setFoo(1);
console.log(foo()); // 1
```

앞서 봤던 클로저의 개념과 매우 유사하게 이해할 수 있다. val 같은 경우에는, useState(0)을 실행한 후, 직접적으로 호출하지 않아 더 이상 접근이 안될 것 같지만, state() 와 setState()에서 사용하는 변수기에 앞서 설명했던 것 처럼 외부함수의 자유변수 즉 val에 접근을 할 수 있다.

이를 React 와 같은 함수형 컴포넌트에서는 다음과 같이 사용할 수 있다.

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  return {
    click: () => setCount(count() + 1),
    render: () => console.log("render:", { count: count() }),
  };
}
const C = Counter();
C.render(); // render: { count: 0 }
C.click();
C.render(); // render: { count: 1 }
```

그리고 Closure를 사용하여 구현할 때 주의해야 될 점은, 함수로 꼭 감싸주어야 된다는 것이다.
다음 함수를 확인해보자

```javascript
function useState(initialValue) {
  var _val = initialValue;
  function setState(newVal) {
    _val = newVal;
  }
  return [_val, setState];
}
var [foo, setFoo] = useState(0);
console.log(foo); // 0
setFoo(1);
console.log(foo); // 0
```

왜 setFoo(1)을 호출했는데 아직 0일까?

> useState의 foo를 destruct 할 때 호출에서 val을 참조하고 변경되지 않기 때문!!

### useEffect Hook

useEffect의 사용 예시를 보면 다음과 같다.

```javascript
useEffect(() => {
  console.log("do something");
}, [someDependency]);
```

해석해보면 someDependency에 변경이 생겼을 때 useEffect의 함수를 실행한다는 것이다.
그럼 마찬가지로 위와 유사하게 closure를 사용하여 구현해보자.

먼저 closure로 저장할 hook에 대한 정보를 만들어야된다.

```javascript
let hooks = [],
  currentHook = 0;
```

여기서 hooks 배열은 closure에 해당하는 부분으로 나의 state들의 정보들을 가지고 있다. `hooks[currentHook]`으로 해당하는 state 정보를 호출 할 수 있다.

이제 실제로 useEffect 함수를 구현을 하면 된다. 2가지 parameter를 받아, 첫번째로는 실행할 callback 함수, 두번째는 dependency 배열을 받는다.
실행 논리는 다음과 같다.

1. 만약 빈 array라면 처음 callback 함수를 실행 후 다시 실행되면 안된다.
2. 내 dependency array가 이전 버전과 다르다면 callback 함수를 실행한다.

이제 javascript로 구현하면 다음과 같다.

```javascript
useEffect(callback, depArray) {
  const hasNoDeps = !depArray; // if dependency is [] run once first time
  const deps = hooks[currentHook]; // closure hook
  const hasChangedDeps = deps // check closure hook and depArray
    ? !depArray.every((el, i) => el === deps[i])
    : true;
  if (hasNoDeps || hasChangedDeps) {
    // call callback when depArray changed or dependency is []
    callback();
    hooks[currentHook] = depArray; // set hook to current dependency
  }
  currentHook++; // increase hook iterator
}
```

이제 마치 React에서 사용할 수 있도록 render와 useState를 함께 붙여보면 다음과 같은 형태로 나오게 된다.

```javascript
const MyReact = (function () {
  let hooks = [],
    currentHook = 0; // hook iterator
  return {
    render(Component) {
      const Comp = Component(); // run effect
      Comp.render();
      currentHook = 0; // init for next render
      return Comp;
    },
    useEffect(callback, depArray) {
      const hasNoDeps = !depArray; // if dependency is [] run once first time
      const deps = hooks[currentHook]; // closure hook
      const hasChangedDeps = deps // check closure hook and depArray
        ? !depArray.every((el, i) => el === deps[i])
        : true;
      if (hasNoDeps || hasChangedDeps) {
        // call callback when depArray changed or dependency is []
        callback();
        hooks[currentHook] = depArray; // set hook to current dependency
      }
      currentHook++; // increase hook iterator
    },
    useState(initialValue) {
      hooks[currentHook] = hooks[currentHook] || initialValue;
      const setStateHookIndex = currentHook; // for setState closure
      const setState = (newState) => (hooks[setStateHookIndex] = newState);
      return [hooks[currentHook++], setState];
    },
  };
})();
```

이를 활용한 Counter hook을 만든다면 다음과 같이 사용할 수 있다.

```javascript
function Counter() {
  const [count, setCount] = MyReact.useState(0);
  const [text, setText] = MyReact.useState("foo"); // second hook state
  MyReact.useEffect(() => {
    console.log("effect", count, text);
  }, [count, text]);
  return {
    click: () => setCount(count + 1),
    type: (txt) => setText(txt),
    noop: () => setCount(count),
    render: () => console.log("render", { count, text }),
  };
}
```

## 결론

물론 실제 React에서 구현된 hook은 이것보다 훨씬 효율적이고 잘 작성되었을 것이다. 여기서는 render함수를 JSX로 받아 DOM에 마운트 하는과정도 건너 뛰었고, 여럿 비효율적인 부분도 많아 보인다. 하지만, 클로저에 대한 개념과 hook의 동작과정을 더 잘 이해할 수 있게 되었다면 그걸로 충분하지 않을까?

참고
https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/

https://poiemaweb.com/js-closure
