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

React에서 가장 많이 사용하는 useState hook을 구현해보자.

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

앞서 봤던 클로저의 개념과 매우 유사하게 이해할 수 있다. val 같은 경우에는, useState(0)을 실행한 후, 직접적으로 호출하지 않아 더 이상 접근이 안될 것 같지만, state() 와 setState()에서

### useEffect Hook

참고
https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/

https://poiemaweb.com/js-closure
