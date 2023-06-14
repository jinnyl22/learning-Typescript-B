# Chapter 03

## 3.1 유니언 타입

유니언이란, 값에 허용된 타입이 2개 이상으로 확장하는 것을 의미한다.

```tsx
let mathematician = Math.random() > 0.5 ? undefined : "Mark Goldberg";
// 타입: undefined | string
```

mathematician은 undefined 혹은 string 타입일 수 있다.

타입스크립트에서 이를 표현하기 위해 유니언 기호, | 를 사용하여 나타낼 수 있다.

```tsx
let singer = Math.random() > 0.5 ? "Red Velvet" : 971011;

singer.toString(); // Ok

singer.toUpperCase(); // Error

singer.toFiexd(); // Error
```

유니언으로 선언한 모든 타입에 존재하는 멤버 속성에만 접근할 수 있다.

## 3.2 내로잉

내로잉이란, 값에 허용된 타입들 중에서 하나를 채택하는 과정을 말한다. 

내로잉 방법

- typeof 연산자로 비교
- 타입 가드
    - 값 할당
    - 조건문 검사

타입 가드란, 타입을 좁히는 데 사용할수 있는 **논리적 검사**를 타입 가드라고 한다.

## 3.3 리터럴 타입

리터럴 타입이란, 좀 더 구체적인 버전의 원시 타입을 말한다.

```tsx
const mathematician = "Mark Goldberg";
// 타입: "Mark Goldberg"
```

## 3.4 엄격한 null 검사

엄격한 null검사를 활성화 시키면, 변수에 undefined 또는 null이 할당될 가능성을 파악하여 에러를 출력한다.

변수에 undefined나 null로 초기화 시키면 추후에 어떤 오류를 범할지 파악하기가 어렵기 때문에 엄격한 null검사를 활성하 시키는 것이 좋다.

타입스크립트에서 falsy로 정의된 값은 문맥상 false 값을 갖고 false, 0, “”, null, undefined, NaN 가 해당된다. 이외의 값은 truthy로 정의되고 문맥상 true 값을 갖는다.

```tsx
let biologist = Math.random() > 0.5 && "Rachel Carson";

if(biologist) {
	bisologist; // 타입: string
}
else {
	biologist; // 타입: string | false
}
```

&&, ? 논리 연산자를 통해 참 여부를 검사할 수 있다.

```tsx
let geneticist = Math.random() > 0.5 ? "Barbara McClintock" : undefined;

geneticist && geneticist.toUpperCase(); // OK
geneticist?.toUpperCase(); // OK
```

초기값이 없는 변수에 대해서, 타입스크립트는 초기값이 할당되기 전까진 undefined임을 알고 있다. 그렇기 때문에 undefined까지 유니온 타입으로 확장하면 Error를 제거할 수 있다.

```tsx
let mathematician: string;

mathematician?.length; // Error

// --------------------------------- 

let mathematician: string | undefined;

mathematician?.length; // OK
```

## 3.5 타입 별칭

타입스크립트에서는 타입에 대하여 alias를 할당할 수 있다. 이를 타입 별칭 및 type alias라고 한다.

아래의 Exercise에서 Person 타입이 그 예시이다.