# Typescript 공부 내용 정리

## 목차

- [1. 소개](#1-소개)
- 개요
  - [2.0 타입스크립트 개요](#20-타입스크립트-개요)
  - [2.1 암시적 타입 vs 명시적 타입](#21-암시적-타입-vs-명시적-타입)
  - [2.2 Typescript의 타입 part1(type 할당 방법, Alias, return 타입 지정)](#22-typescript의-타입-part1)
  - [2.3 Typescript의 타입 part2(readonly, Tuple, any)](#23-typescript의-타입-part2)
  - [2.4 Typescript의 타입 part3(unknown, void, never)](#24-typescript의-타입-part3)
- 함수
  - [3.0 콜 시그니처(Call Signatures)](#30-콜-시그니처call-signatures)
  - [3.1 오버로딩(Overloading)](#31-오버로딩overloading)
  - [3.2 다형성(Polymorphism)](#32-다형성polymorphism)
  - [3.3 제네릭 이해하기](#33-제네릭-이해하기)
  - [3.4 제네릭 사용 사례](#34-제네릭-사용-사례)

## 1. 소개

타입스크립트는 개발자가 하는 실수들을 줄여준다.

vscode 와 함께 사용하기를 정말 정말 추천  
→ ms가 typescript, vscode를 만들었다. 둘의 궁합이 좋다. 개발경험, 자동완성 등이 잘 맞는다.

node.js 버전은 17.3.1 이상이면 충분하다.

타입스크립트가 제공하는 제일 큰 장점은 타입 안정성이다.  
타입 안정성 덕분에 코드에 버그가 엄청 줄게된다. 런타임 에러가 줄고, 생산성이 늘어난다.

자바스크립트의 문제는 잘못된 코드를 작성해도 에러가 발생하지 않는 것이다.

```jsx
function devide(a, b) {
  return a / b;
}
devide(2, 3); // 0.666666
devide("xxxx"); // NaN(에러는 발생하지 않음)
```

자바스크립트는 그저 코드를 실행할 뿐, 다음과 같은 에러를 알려주지 않는다.

- 입력값이 두 개인데 하나만 보냈어!
- 문자열 대신 숫자를 보내야 해!

```jsx
const ara = { name: "ara" };
ara.hello(); // ara.hello is not a function 에러 발생
```

자바스크립트는 실행 시점에 런타임 에러를 발생시킨다.

언어가 자체적으로 ara 객체를 분석해서 hello가 없다는 것을 알려주는 것이 이상적이지만 자바스크립트에서는 못 한다. 이렇기 때문에 자바스크립트를 사용하면 코드에 어떤 에러가 있는지 나중에 발견된다.  
이상적으로는 코드가 실행되기 전에 실수를 바로잡아야 한다.

타입스크립트를 사용하면 이런 이상한 코드를 작성 수 없다.  
코드가 실행되기도 전에 에러를 알려준다.

## 2.0 타입스크립트 개요

### 타입스크립트가 하는 일

타입스크립트란 strongly typed programming language(강 타입 프로그래밍 언어)다.

타입스크립트는 자바스크립트를 생성한다. 브라우저가 자바스크립트를 이해하기 때문이다.

Node.js는 타입스크립트를 이해할 수 있다.

타입스크립트가 에러가 발생할 것 같은 코드를 발견하면 자바스크립트로 변환하지 않는다.

타입스크립트 플레이그라운드에서 확인해보기([https://www.typescriptlang.org/play](https://www.typescriptlang.org/play))

```tsx
const ara = {
  nickname: "kimbab",
};

ara.hello(); // hello에 빨간 밑줄
```

hello에서 에러가 난다.  
Errors in code: Property 'hello' does not exist on type '{ nickname: string; }'.

```tsx
[1, 2, 3, 4] + false; // 전체에 빨간 밑줄
```

자바스크립트는 이 코드가 실행되게 두었지만, 타입스크립트는 에러를 알려주고 컴파일되지 않는다.  
Errors in code: Operator '+' cannot be applied to types 'number[]' and 'boolean'.

```tsx
function devide(a, b) {
  // a, b 에 빨간 밑줄
  return a / b;
}
devide("xxxx"); // 전체에 빨간 밑줄
```

타입스크립트는 코드가 실행되기전에 에러를 알려준다.

Errors in code:

- Parameter 'a' implicitly has an 'any' type.
- Parameter 'b' implicitly has an 'any' type.
- Expected 2 arguments, but got 1.

a, b가 어떤 타입인지 몰라서 우리를 보호해줄 수 없다고 한다. 그리고 devide 함수에 두 개의 입력값이 있어야 하지만 하나만 제공되었다고 알려준다.

우리는 a, b가 숫자라고 알려줄 필요가 있다.

```tsx
const player = {
  age: 12,
};

player.age = false; // player.age에 빨간 밑줄
```

타입스크립트가 타입을 추론해서 에러를 표시한다.  
Errors in code: Type 'boolean' is not assignable to type 'number'.

타입스크립트를 사용했을 뿐인데 공짜로 이런 보호장치가 생긴다. 이게 바로 사람들이 타입스크립트를 좋아하는 이유다.

## 2.1 암시적 타입 vs 명시적 타입

### 타입 시스템에 대해서

데이터와 변수의 타입을 명시적으로 정할 수 있고, 정하지 않으면 타입스크립트가 추론을 한다.

### 암시적 타입

```tsx
let a = "hello";
a = "bye";
a = 1; // 에러: Type 'number' is not assignable to type 'string'.
```

### 명시적 타입

```tsx
let b: boolean = "x"; // 에러: Type 'string' is not assignable to type 'boolean'.
```

`: boolen`이 부분이 진짜 typescript다.  
변수 b가 boolean이라는 것을 타입스크립트에게 알려준 것이다. (Type Checker와 소통하는 방식)

: boolean 을 사용하는 것을 추천하지는 않는다.  
Type Checker에게 타입 추론하는 것을 맡긴다. 더 쉽고 코드가 짧아져서 가독성이 좋다.

### 타입 추론

```tsx
let c: number[] = [];
let d = [1, 2, 3];
```

d는 숫자 배열인 것을 타입 스크립트가 추론한다.  
c는 typescript가 추론을 하지 못한다. 이 경우에 명시적 표현이 유용하다.

number 타입을 지정하지 않으면 any 타입이 된다.

보통 명시적 표현은 최소한으로 사용하는게 좋다. 타입스크립트가 추론을 하도록 두는 것이 좋다.  
코드를 작성하는 시간이 줄어든다.

### 타입 추론 확인

```tsx
const player = {
  name: "ara",
};
player.name = 1; // 에러
player.hello(); // 에러
```

vscode에서 player에 마우스 오버 하면 player 객체에서 타입이 추론된 것을 확인할 수 있다.  
`const player: { name: string; }`  
그 다음 줄에 player를 입력하고 .(점)을 찍으면 자동완성에 name이 보인다.  
player.name에 1을 할당하면 에러가 난다.  
hello를 입력하면 객체에 hello가 없기 때문에 에러가 난다.

이 모든 에러가 추론이 동작했기 때문에 발생한 것이다.

## 2.2 Typescript의 타입 part1

> const나 let 같은 변수에 type을 할당하는 방법(const [이름]: [타입])  
> 선택적 타입을 생성하는 방법([이름]?: [타입])  
> Alias 타입을 생성하는 방법  
> argument와 함수 return 타입을 생성하는 방법  
> 화살표 함수에서 타입을 생성하는 방법

### 변수에 type을 할당하는 방법

타입은 number, string, boolean이 있고, 타입 배열은 number[] 이런식으로 타입에 배열을 붙여 준다.  
`let a = [1, 2];`  
`let b : string[] = ['1', '2'];`  
변수를 생성할 때 꼭 타입을 지정하지 않아도 된다. 가능한 타입스크립트가 추론하는 것이 좋다.

질문!  
player가 name은 항상 가지고 있고, age는 가지고 있을 수도 있고 아닐 수도 있다.  
이것을 타입스크립트와 어떻게 소통해야 할까?

```tsx
const player: {
  name: string;
  age?: number; // optional parameter
} = {
  name: "ara",
};
```

age가 있을 수도 있고 아닐 수도 있다면 ?(물음표)를 붙여 준다.  
player에 마우스를 올리면 타입 추론이 number | undefined로 된 것을 볼 수 있다.

```tsx
// 위 코드에 이어서
if (player.age < 10) {
  // 에러: 'player.age' is possibly 'undefined'.
}
```

타입스크립트는 player.age가 undefined일 수도 있다고 말해준다.  
아래 코드처럼 player.age가 존재하는지 확인을 거쳐야 한다.(타입스크립트의 보호장치 작동)

```tsx
// player.age 값 존재 여부 확인하는 코드 추가
if (player.age && player.age < 10) {
}
```

### 타입 별칭(Alias) 사용

타입이 반복될 때 Alias를 생성해서 코드 양을 줄일 수 있다. 첫 글자는 대문자로 한다.

```tsx
type Player = {
  name: string;
  age?: number;
};

const ara: Player = {
  name: "ara",
};

const mara: Player = {
  name: "mara",
};
```

타입을 재사용할 수 있다.

객체 뿐만 아니라 다음과 같이 하나의 타입도 Alias로 재사용 할 수 있다. 과도한 사용은 비추천.

```tsx
type Age = number;
type Name = string;
type Player = {
  name: Name;
  age?: Age;
};
```

### return 타입 지정

함수의 return 타입을 미리 알면 자동 완성과 더 많은 보호장치를 준다.

return 타입을 지정하지 않으면 타입스크립트가 타입을 추론한다.

```tsx
function playerMaker(name: string) {
  return {
    name,
  };
}

const ara = playerMaker("ara");
ara.age = 12; // 에러: Property 'age' does not exist on type '{ name: string; }'.
```

playerMaker의 return 타입이 `{ name: string; }`으로 추론되었기 때문에 ara.age에 값을 할당하는 코드에서 에러가 발생한다.

return 타입을 지정하는 방법  
변수에 타입 지정한 방법과 동일하게 함수명(입력값) 뒤에 `: [타입]` 을 추가한다.

```tsx
type Age = number;
type Name = string;
type Player = {
  name: Name;
  age?: Age;
};

function playerMaker(name: string): Player {
  // return 타입 추가
  return {
    name,
  };
}

const ara = playerMaker("ara"); // 함수 시그니처: function playerMaker(name: string): Player
ara.age = 12;
```

return 타입을 추가하면 ara의 타입이 Player라는 것을 알고 있어서 ara의 age를 변경하도록 허용한다.

### 화살표 함수에 타입을 지정하는 방법

```tsx
type Age = number;
type Name = string;
type Player = {
  name: Name;
  age?: Age;
};

// 화살표 함수
const playerMacker = (name: string): Player => {
  return {
    name,
  };
};

const ara = playerMaker("ara"); // 함수 시그니처: function playerMaker(name: string): Player
ara.age = 12;
```

### 정리

타입을 지정하는 방법은 ‘ 콜론 다음에 타입’을 입력한다.  
인수에서 이름을 쓰고 타입을 쓰거나, 함수 뒤에 타입을 써주면 된다.

## 2.3 Typescript의 타입 part2

> readonly
> Tuple
> undefined, null
> any

### readonly 속성

요소를 읽기전용으로 만든다.

player의 name을 수정하지 못하게 하려면 readonly 속성을 추가하면 된다.  
타입스크립트가 name을 변경하지 못하도록 보호장치를 해준다.

```tsx
type Age = number;
type Name = string;
type Player = {
  readonly name: Name; // reaadonly 지정
  age?: Age;
};

const playerMaker = (name: string): Player => ({ name });
const ara = playerMaker("ara");
ara.age = 12;
ara.name = "mara"; // 에러: Cannot assign to 'name' because it is a read-only property.
```

배열에 readonly 지정

```tsx
const names: readonly string[] = ["1", "2"];
names.push(); // 에러: Property 'push' does not exist on type 'readonly string[]'.
names.map; // 에러 없음
names.filter; // 에러 없음
```

배열을 변경하는 push 메소드를 사용하면 에러가 발생한다.  
배열을 변경하지 않는 map이나 filter 메소드는 사용 가능하다.  
타입스크립트의 보호장치로 배열의 불변성(immutability)을 가질 수 있다.

### Tuple

array를 생성하는 것이다.  
최소한의 길이를 가져야하고, 특정 위치에 특정 타입이 있어야 한다.

```tsx
// ["ara", 12, false] // string, number, boolean

const player: [string, number, boolean] = [];
// 에러: Type '[]' is not assignable to type '[string, number, boolean]'. Source has 0 element(s) but target requires 3.
```

튜플은 항상 정해진 갯수의 요소를 가져야하는 array를 지정할 수 있다. 순서에 맞는 타입을 가져야 한다.

어디에서 사용할 수 있을까?  
가끔 api가 이런 array를 줄 때도 있다. 그러면 api 문서를 보고 각 값의 타입을 지정하면 된다.

내가 경험한 예시로는, 차트 라이브러리에 데이터를 넣을 때 [’컬럼제목’, 값, 값, 값, 값] 같은 구조로 넣도록 되어있다. 이 때 첫 번째 값은 string으로 나머지 값을 number로 지정할 때 사용할 수 있을 것 같다.

타입스크립트의 보호장치

```tsx
const player: [string, number, boolean] = ["ara", 12, false];
player[0] = 1; // 에러: Type 'number' is not assignable to type 'string'.
```

타입스크립트가 첫번 째 index는 항상 string이어야 한다는 것을 알기 때문에 number를 할당하는 코드를 허용하지 않는다.

readonly와 tuple을 함께 사용

```tsx
const player: readonly [string, number, boolean] = ["ara", 12, false];
player[0] = "hi"; // 에러: Cannot assign to '0' because it is a read-only property.
```

player의 아무 것도 바꿀 수 없다. 단, 이런 보호장치를 가지는 것은 타입스크립트에서만 가능하다.  
자바스크립트에는 tuple도 readonly도 없다. array만 있다.  
타입스크립트를 사용하면, 코드를 저장하고 실제 production 환경으로 push하기 전에 오류를 확인할 수 있다는점이 멋진 것이다.

### undefined, null

자바스크립트 타입이기도 한 undefined, null 타입이 타입스크립트에도 있다.

선택적 타입을 사용할 때, 선택적 타입은 undefined가 될 수 있다.

```tsx
type Player = {
  age?: number; // (property) age?: number | undefined
};
```

### any

비어있는 값을 쓰면 기본적으로 any가 된다.

```tsx
let a = []; // let a: any[]
```

any는 타입스크립트로부터 자유롭고 싶을 때 쓰는 타입이다.  
any를 사용하면 타입스크립트의 보호장치가 동작하지 않는다. 개발자들의 실수를 잡아주지 않는다.

```tsx
// any 사용
const a: any[] = [1, 2, 3, 4];
const b: any = true;
a + b; // 에러 발생하지 않음
```

```tsx
// any 제거
const a = [1, 2, 3, 4];
const b = true;
a + b; // 에러: Operator '+' cannot be applied to types 'number[]' and 'boolean'.
```

any 사용을 권장하지 않는다.  
타입스크립트 설정에 any 타입을 막기 위한 규칙이 몇가지 있다.

### 정리

readonly 속성과 Tuple 타입 타입스크립트에만 있다.(자바스크립트에 없다.)  
any는 타입스크립트의 보호장치를 해제시킨다. 그래서 자주 쓰지 않도록 해야 한다.  
하지만 탈출구가 존재한다는 것을 알아두는 것은 좋다.

## 2.4 Typescript의 타입 part3

> void, never, unknown

타입스크립트의 핵심은 Type Checker와 소통하는 것이다.  
내가 코드의 타입을 설명해주면, 타입스크립트가 잘못된 코드를 작성할 때 보호해준다.

### unknown

어떤 타입인지 모를 때 unknown 타입을 사용한다. (ex. api로 응답을 받는데 응답 값을 모를 때)  
unknown 타입을 사용하면 타입스크립트로부터 보호를 받게 된다.

```tsx
let a: unknown;

let b = a + 1; // 'a' is of type 'unknown'.
a.toUpperCase; // 'a' is of type 'unknown'.
```

위 코드에서 a의 타입을 모르기 때문에 타입스크립트가 코드를 허용하지 않는다.

타입 체크 후에 a를 사용할 수 있다.

```tsx
let a: unknown;

if (typeof a === "number") {
  // a가 number인 것을 확인
  let b = a + 1;
}

if (typeof a === "string") {
  // a가 string인 것으 확인
  let b = a.toUpperCase();
}
```

`if(typeof a === 'number')` 에서 a에 마우스를 올리면 unknown 타입으로 보이지만, `let b = a + 1;` 에서는 a가 number 타입으로 보인다.  
타입이 확인되었기 때문에 타입스크립트가 해당 코드를 허용한다.

이처럼 변수의 타입을 미리 알지 못 할 때 unknown을 사용한다.  
그러면 타입스크립트가 타입 확인을 강제로 시킬 것이다.

### void

void는 비어있는 것을 뜻한다. 아무것도 return하지 않는 함수에 사용된다.  
따로 지정해줄 필요는 없고, 타입스크립트가 자동으로 인식한다.

```tsx
function hello() {
  console.log("x");
}
```

hello에 마우스 오버하면 `function hello(): void`가 보인다.

```tsx
function hello() {
  console.log("x");
}
const a = hello();
a.toUpperCase(); // 에러: Property 'toUpperCase' does not exist on type 'void'.
```

### never

never는 함수가 절대 return하지 않을 때 발생한다. (예를들어, 함수에서 exception(예외)가 발생할 때)

return값이 있는 함수의 return 타입을 never로 지정하면 오류가 생긴다.

```tsx
function hello(): never {
  return "x"; // 에러: Type 'string' is not assignable to type 'never'.
}
```

never 사용 예시

```tsx
function hello(): never {
  throw new Error("x");
}
```

return 타입이 never인 함수 내부에서 오류를 발생시키면 정상 동작한다.  
오류는 return하지 않기 때문이다. 이럴 때 never를 사용한다.

never가 발생하는 상황

```tsx
function hello(name: string | number) {
  if (typeof name === "string") {
    name; // string
  } else if (typeof name === "number") {
    name; // number
  } else {
    name; // never
  }
}
```

else 안에 있는 name은 never가 된다.

name 입력값을 string 또는 number 타입으로 받는 함수에서 if와 else if에서 string 타입과 number 타입을 체크해주었기 때문에, else의 코드는 절대(never) 실행되지 않아야 한다.

### 정리

unknown은 어떤 타입인지 모를 때 사용한다.  
void는 아무것도 return하지 않는 함수의 return 타입에 사용한다.  
never는 예외를 발생하는 함수나 실행되면 안되는 코드에서 발생한다.

가장 많이 사용하게 될 것 같은 건 void, 그 다음을 unknown이다.  
never는 거의 사용하지 않을 것이지만 알아두는 게 좋다. 마우스를 올렸을 때 보게 될 수 있기 때문이다.

## 3.0 콜 시그니처(Call Signatures)

함수를 어떻게 호출해야할 지 알려주는 정보다. 인자(arguments)의 타입과 리턴 타입을 알려준다.  
함수에 마우스 올리면 볼 수 있다.

```tsx
const add = (a: number, b: number) => a + b;
// 콜 시그니처: const add: (a: number, b: number) => number
```

함수의 call signature 타입을 만드는 방법

```tsx
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => a + b;
```

함수를 구현하기 전에 함수가 어떻게 작동하는지 서술해 둘 수 있다.  
코드를 구현하면서 타입을 선언하지 않아도 된다.

콜 시그니처를 작성하고 함수를 만들면, 타입스크립트가 타입 정보를 알고있기 때문에 매개변수나 리턴 타입이 잘못되었을 때 알려준다.

```tsx
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => {
  a + b;
}; // 에러
```

Error in code

- Type '(a: number, b: number) => void' is not assignable to type 'Add'. Type 'void' is not assignable to type 'number'.

## 3.1 오버로딩(Overloading)

오버로딩은 함수가 서로 다른 여러 개의 시그니처를 가지고 있는 것이다.  
직접 작성하는 경우는 많지 않을 것인데, 우리가 많이 사용하는 라이브러리들에서 오버로딩을 많이 사용한다.

콜 시그니처 작성 방법

```tsx
// 방법 1
type Add = (a: number, b: number) => number;

// 방법 2
type Add = {
  (a: number, b: number): number;
};
```

방법 1은 간단하게 만드는 방법이다.
방법 2처럼 길게 작성할 수도 있는데, 이 방법은 오버로딩 때문에 존재한다.

### 오버로딩 예시 1(parameters 타입이 다른 경우)

next.js에서 Router의 push 메소드에는 string을 넘길 수도 있고, object 넘길 수도 있다. 둘다 동작한다.

```tsx
// next.js
Router.push("/home");

Router.push({
  path: "/home",
  state: 1,
});
```

push는 다음과 같이 구성되어있을 것이다.

```tsx
type Config = {
  path: string;
  state: object;
};
type Push = {
  (path: string): void;
  (oconfig: Config): void;
};

const push: Push = (config) => {
  if (typeof config === "string") console.log(config);
  else {
    console.log(config.path);
  }
};
```

오버로딩은 패키지나 라이브러리를 디자인할 때 유용하게 사용된다.

### 오버로딩 예시 2(parameters 개수가 다른 경우)

```tsx
type Add = {
  (a: number, b: number): number;
  (a: number, b: number, c: number): number;
};

const add: Add = (a, b, c) => {
  // 에러
  return a + b;
};
```

Error in code

- Type '(a: number, b: number, c: number) => number' is not assignable to type 'Add'. Target signature provides too few arguments. Expected 3 or more, but got 2.

에러나지 않도록 수정

```tsx
type Add = {
  (a: number, b: number): number;
  (a: number, b: number, c: number): number;
};

const add: Add = (a, b, c?: number) => {
  if (c) return a + b + c;
  return a + b;
};

add(1, 2);
add(1, 2, 3);
```

Add의 콜 시그니쳐가 의미하는 바는 c는 옵셔널이라는 것이다.  
add의 세 번째 인자를 옵셔널로 변경하고 타입을 지정해줘야 한다. `c?: number`  
c가 있을 때의 처리를 해준다. `if(c) return a + b + c;`

## 3.2 다형성(Polymorphism)

poly는 many, multi라는 뜻이고, morphos는 form, structure를 뜻한다.  
Polymorphism은 다양한 형태를 말한다.

배열의 값을 프린트해주는 함수를 만든다면, 배열에는 다양한 타입이 올 수 있다.

```tsx
type SuperPrint = {
  (arr: number[]): void;
  (arr: boolean[]): void;
  (arr: string[]): void;
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

superPrint([1, 2, 3, 4]);
superPrint([true, false, true]);
superPrint(["a", "b", "c"]);
```

위 코드도 잘 동작하지만 다양한 상황에서 사용하려면 콜 시그니처레 모든 가능성을 추가해 놓아야 한다.  
ex) `superPrint([1, 2, true, false])`를 실행하려면 콜 시그니처에 `(arr: (number|boolean)[]): void`를 추가해야 한다.

다형성을 활용하는 더 좋은 방법이 있다. generic을 사용하는 것이다.  
제너릭이란 타입의 placeholder 같은 것이다.

콜 시그니처를 작성할 때 확실한 타입을 모른다면 타입스크립트가 타입을 유추하도록 한다.

```tsx
type SuperPrint = {
  <TypePlaceholder>(arr: TypePlaceholder[]): void;
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

superPrint([1, 2, 3, 4]); // 콜 시그니처: const superPrint: <number>(arr: number[]) => void
superPrint([true, false, true]); // 콜 시그니처: const superPrint: <boolean>(arr: boolean[]) => void
superPrint(["a", "b", "c"]); // 콜 시그니처: const superPrint: <string>(arr: string[]) => void
superPrint([1, 2, true, false]); // 콜 시그니처: const superPrint: <number | boolean>(arr: (number | boolean)[]) => void
```

타입스크립트는 함수 실행 라인에서 배열의 타입을 확인한다.  
superPrint의 콜 시그니처에서 TypePlaceholder는 타입스크립트가 유추한 타입으로 바뀐다.

이게 제네릭의 핵심이다.  
**콜 시그니처에 타입을 일일이 써주지 않아도 함수에 여러 타입을 사용하는 것을 허용한다.**

return 타입에도 적용해보자.

```tsx
type SuperPring = {
  <TypePlaceholder>(arr: TypePlaceholder[]): TypePlaceholder;
};

const superPrint: SuperPring = (arr) => arr[0];

const a = superPrint([1, 2, 3, 4]);
const b = superPrint([true, false, true]);
const c = superPrint(["a", "b", "c"]);
const d = superPrint([1, 2, true, false, "hello"]); // 여기!
```

맨 마지막 줄에서 superPrint의 콜 시그니처는 `const superPrint: <string | number | boolean>(arr: (string | number | boolean)[]) => string | number | boolean` 이다.  
d의 타입은 `string | number | boolean` 로 추론된다.

TypePlaceholder 대신에 무엇이든 올 수 있지만 주로 T를 사용한다.

### 정리

`type SuperPring = {<T>(arr: T[]): T}` 코드 설명

- 콜 시그니처에 제네릭을 사용해서 타입스트립트가 타입을 유추하도록 한다.
- 유추한 타입의 배열이 인자로 들어올 것이고, 그 타입 중 하나를 리턴하도록 한다.

## 3.3 제네릭 이해하기

함수 시그니처에 다양한 타입을 사용하고 싶다면 any로 해도 되는 것 아닐까?
→ any를 사용하면 타입스크립트의 보호장치를 사용할 수 없다.

```tsx
type SuperPrint = (arr: any[]) => any;

const superPrint: SuperPrint = (arr) => arr[0];

const d = superPrint([1, 2, true, false, "hello"]);

d.toUpperCase(); // 에러 없음
```

d는 number 타입이고, toUpperCase()는 number 타입에서 사용할 수 없어서 런타임 환경에서 에러가 발생할 수 있는 코드이다. any를 사용했기 때문에 타입스크립트가 동작하지 않는다.

제네릭을 사용하려면 any를 T로 변경하고, 콜 시그니처 맨 앞에 <T>를 써준다.

```tsx
type SuperPrint = <T>(arr: T[]) => T;

const superPrint: SuperPrint = (arr) => arr[0];

const d = superPrint([1, 2, true, false, "hello"]);

d.toUpperCase(); // 에러
```

타입스크립트가 `d.toUpperCase()` 코드를 허용해주지 않는다.

Error in code

- Property 'toUpperCase' does not exist on type 'string | number | boolean'. Property 'toUpperCase' does not exist on type 'number'.

제네릭은 내가 요구한대로 signature를 만들어 준다. 다양한 타입의 시그니처를 내가 일일이 작성할 필요가 없다.

타입스크립트는 제네릭이 처음 사용되는 지점을 기반으로 타입이 무엇인지 알게 된다.

```tsx
type SuperPrint = <T, M>(a: T[], b: M) => T;

const superPrint: SuperPrint = (arr) => arr[0];

const d = superPrint([1, 2, 3, 4], "x");
```

위 함수가 실행되는 시점에 a로 들어온 배열의 타입이 T가 되고, b로 들어온 값의 타입이 M이 된다.  
superPrint의 콜 시그니처는 `const superPrint: <number, string>(a: number[], b: string) => number`가 된다.  
맨 앞에 있는 `<T, M>`은 제네릭의 이름을 알려주는 부분이다.

## 정리

타입스크립트는 우리의 요구에 따라 call signature를 생성한다.  
우리는 generic으로 원하는 signature를 알려주기만 하면 된다.

## 3.4 제네릭 사용 사례

다른 라이브러리를 사용할 때 제네릭을 사용한다. next.js, react를 한다면 제네릭을 사용하게 될 것이다.  
함수의 콜 시그니처 외에 제네릭을 사용되는 곳을 알아보자.

### 함수 작성 시, 제네릭 사용

```tsx
// type SuperPrint = <T,M>(a: T[], b: M) => T
// const superPrint: SuperPrint = (arr) => arr[0]

// 위 코드 대신에 함수에 제네릭 사용
function superPrint<T>(arr: T[]) {
  return arr[0];
}

const d = superPrint([1, 2, 3, 4]);
```

### 제네릭 타입 오버라이딩(타입스크립트에 타입 명시)

```tsx
function superPrint<T>(arr: T[]) {
  return arr[0];
}

const d = superPrint<boolean>([1, 2, 3, 4]);
```

타입을 명시하는 것은 좋지 않은 방법이다. 타입스크립트가 유추하도록 하는 것이 제일 좋다.  
위 코드처럼 `<boolean>` 으로 타입을 명시하면, `<T>` 에 적용되어서 인자가 `(arr: boolean[])` 가 된다.

### 타입 확장

타입을 생성하고 그 타입을 또다른 타입에 넣어서 사용할 수 있다.

```tsx
type Player<E> = {
	name: string
	extraInfo: E
}

const ara: Player<{favFood: string}> = {
	name: 'ara'
	extraInfo: {
		favFood: 'kimbab'
	}
}
```

```tsx
type Player<E> = {
	name: string
	extraInfo: E
}

type AraPlayer = Player<{favFood: string}> // 타입 분리

const ara: AraPlayer> = {
	name: 'ara'
	extraInfo: {
		favFood: 'kimbab'
	}
}
```

```tsx
type Player<E> = {
	name: string
	extraInfo: E
}

type AraExtra = {favFood: string} // 타입 분리
type AraPlayer = Player<AraExtra>

const ara: AraPlayer> = {
	name: 'ara'
	extraInfo: {
		favFood: 'kimbab'
	}
}
```

### 타입 재사용

많은 것들이 들어있는 타입을 하나 가지고 있고, 그 중 하나가 달라질 수 있는 타입이라면 제네릭을 사용하면 된다. 그러면 재사용 할 수 있다.

```tsx
type Player<E> = {
	name: string
	extraInfo: E
}

type AraExtra = {favFood: string}
type AraPlayer = Player<AraExtra>

const ara: AraPlayer> = {
	name: 'ara'
	extraInfo: {
		favFood: 'kimbab'
	}
}

const mara: Player<null> = { // Player 재사용
	name: 'mara',
	extraInfo: null
}
```

### 배열에서 사용

제네릭을 배열에서도 사용할 수 있다.  
vscode에서 ts파일을 만들고, Array를 입력하면 툴팁에 Array<T>가 보인다.  
typescript에서 Array를 생성할 때 제네릭을 받고 있음을 알 수 있다.

```tsx
type a = Array<number>;

let a: A = [1, 2, 3, 4];
```

`Array<number>` 이런 형태의 제네릭을 많이 볼 것이다.  
이것도 제네릭을 사용하는 방법이다.

```tsx
function printAllNumbers(arr: Array<number>) {}
```

arr이 number 배열임을 나타낼 때 `number[]`를 사용하기도 하지만 `Array<number>` 를 사용하기도 한다.

### React에서 사용

React에서 useState 함수는 제네릭을 받는다.  
아래 코드처럼 제네릭을 넣어주면 state는 number 타입이 된다.

```tsx
useState<number>();
```
