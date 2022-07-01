---
layout: post
title: 왜 TypeScript인가
subtitle: React에서 TypeScript를 사용하는 이유
categories: TIL
tags: [TIL, Javascript, TypeScript]
---

## TypeScript

### Why TypeScript

JavaScript는 Dynamic Typing을 지원한다. [관련블로그](https://seongonion.tistory.com/16)

간략하게 설명하면, 변수를 지정시에 데이터 타입등을 정하지 않고, 컴퓨터가 정하도록 둘 수 있다. 코드가 간결하고 로직을 명확하게 볼 수 있지만, 컴퓨터가 파악하기에 실행속도가 느려지가 해당 오류가 날 수 있다. 그걸 TypeScript를 이용하여 정적 타이핑으로 만들어 주는 것이다. 이런 데이터 타입을 지정해 줌으로써, 코드의 안정성과 정교함이 커지게 된다.

예제

```javascript
5 - "3"; //?
1 + true; //?
```

```javascript
function mul(a, b) {
  return a * b;
}
mul(); //?
mul("hi"); //?
mul(true, "hi"); //? -> Runtime Error
```

```typescript
var count = 0;
count = "1"; //Type 'string' is not assignable to type 'number'
```

```javascript
var obj = {
  append: 123,
};
obj.apend; //undefined
```

```typescript
var obj = {
  append: 123,
};
obj.apend; //'append'아니야? 그거 2번줄에 declared ㅋㅋ
```

```typescript
function mul(a: number, b: number): string {
  //return도 지정 가능
  return (a * b).toString();
}
console.log(mul(3, 4));
```

### Types

#### Implicit Types [타입추론]

```typescript
let a = "test";
a = 3; //string -> number 안됨!
let b = ["a", "b", "c"];
b.push("d"); //OK
```

```typescript
const fire = {
    member1 : 'juhyeong';
    member2 : 'yeonggi';
    member3 : 'haryung';
}
fire.member1 = 42 //ERROR
```

#### Explicit Types [타입지정]

```typescript
let c: number[] = [];
c.push(1); //OK
```

#### NULL 처리

```typescript
const yeonggi: {
  team: string;
  age?: number;
} = {
  team: "FIRE",
};
if (yeonggi.age < 10) {
  //ERROR
  console.log(yeonggi.age);
}
```

```typescript
type Member = {
  name: string;
  age?: number;
};
function memberGenerator(name: string) {
  //?
  return {
    name,
  };
}
// const memberGenerator = (name: string) : Player => ({name})
const yeonggi = memberGenerator("yeonggi");
yeonggi.age = 12;
```

#### ANY

```typescript
const a: any[] = [1, 2, 3, 4];
const b: any = true;

a + b; //OK but hmmm...
```

```typescript
let a: unknown;
if (typeof a === string) {
  let b = a + "1";
}
if (typeof a === number) {
  let b = a + 1;
}
```

[never 관련 블로그](https://ui.toast.com/weekly-pick/ko_20220323)

### Generic

### Type vs Interface
