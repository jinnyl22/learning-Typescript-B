# Chapter 04

## 4.1 객체 타입

객체 리터럴을 생성하면, 해당 객체는 객체의 값(value)과 동일한 속성명과 원시 타입을 갖는다. 값의 속성에 접근하려면 value[’멤버’] 또는 value.멤버 구문을 사용한다.

```tsx
let wizard = {
	born: 1980,
	name: "Harry Potter",
};

wizard.['born']; // 타입: number
wizard.name; // 타입: string

wizard.house; // Error
```

Q. null과 undefined를 제외한 모든 값은 그 값에 대한 실제 타입의 멤버 집합을 가지므로 타입스크립트는 모든 값의 타입을 확인하기 위해 객체 타입을 이해해야 합니다..?

객체 타입 선언은 필드 값 대신에 타입을 사용한다.

```tsx
let wizard: {
	born: number,
	name: string,
};

wizard = {
	born: 1980,
	name: "Harry Potter",
};
```

객체 타입을 계속 작성하는 일을 방지하기 위해서 타입 얼라이어스를 응용할 수 있다.

```tsx
type wizard = { // 타입 얼라이어스
	born: number,
	name: string,
};

let 주인공: wizard; // wizard 타입

주인공 = {
	born: 1980,
	name: "Harry Potter",
};

console.log(주인공); // { "born": 1980, "name": "Harry Potter" }
console.log(주인공['born']); // 1980
console.log(주인공['name']); // "Harry Potter"
```

## 4.2 구조적 타이핑

매개변수나 변수가 특정 객체 타입으로 선언되면, 
타입스크립트에서 어떤 객체를 사용하든 해당 속성이 있어야 한다. (cf. 자바스크립트는 덕 타이핑을 사용)

타입스크립트의 타입 시스템은 구조적 타입화(structurally typed) 되어있다.

```tsx
type WithFirstName = {
    firstName: string;
};

type WithLastName = {
    lastName: string;
};

const hasBoth = {
    firstName: "Lucille",
    b: "Clifton",
    num: 1,
};

let withfirstName: WithFirstName = hasBoth;
let withlastName: WithLastName = hasBoth; // Error: Property lastName is missing..

console.log(withfirstName); 
/*
	{
	  "firstName": "Lucille",
	  "b": "Clifton",
	  "num": 1
	}
*/
```

### **사용 검사**

할당하는 값에는 객체 타입의 필수 속성이 있어야 한다. 그렇지 않으면 타입 오류가 발생한다.

```tsx
type FirstAndLastNames = { // 객체 타입
	first: string;
	last: string;
};

const hasBoth: FirstAndLastNames = { // 객체 생성
	first: "kakao",
	last: "talk",
};

const hasOnlyOne: FirstAndLastNames = { 
	first: "Fukuoka",
	// Error: last is missing in type...
};
```

### **초과 속성 검사**

변수가 객체 타입으로 선언되고, 초기값에 객체 타입에서 정의된 것보다 많은 필드가 있다면 타입스크립트에서 타입 오류가 발생한다.

초과 속성 검사는 객체 타입으로 선언된 위치에서 생성되는 객체 리터럴에 대해서만 일어난다. 기존 객체 리터럴을 제공하면 초과 속성 검사를 우회할 수 있다.

```tsx
type Poet = { // Poet 객체 타입 별칭
	born: number;
	name: string;
};

const poetMatch: Poet = { // Poet 객체 타입의 poetMatch 객체 선언
	born: 1928,
	name: "Maya Angelou",
};

const extraProperty: Poet = { // 타입 애너테이션 부분에서 Poet 객체 타입의 필드가 초과되었는지 검사
	activity: "walking", // Error
	born: 1935,
	name: "Mary Oliver",
};
```

```tsx
const extraPropertyObject = { 
	activity: "walking",
	born: 1935,
	name: "Mary Oliver",
};

const extraPropertyOk: Poet = extraPropertyObject; // OK
```

### **중첩된 객체 타입**

기본 필드명 대신에 { } 객체 타입을 사용한다.

중첩된 객체의 타입을 자체 타입 별칭으로 할당 할 수 있다.

```tsx
type Poem = { // Poem 객체 타입 별칭
	author: {
		firstName: string;
		lastName: string;
	};
	name: string;
};

const poemMatch: Poem = { // 객체 선언
	author: {
		firstName: "Sylvia",
		lastName: "Plath",
	},
	name: "Lady Lazarus",
}

const poemMismatch: Poem = { // 객체 선언
	author: {
		name: "Sylvia Plath", // Error: 구조적 타이핑에 위배
	},
	name: "Tulips",
}
```

```tsx
type Author = {
	firstName: string;
	lastName: string;
};

type Poem = {
	author: Author;
	name: string;
};

const poemMismatch: Poem = { // 객체 선언
	author: {
		name: "Sylvia Plath", // 역시나 Error: 구조적 타이핑에 위배
	},
	name: "Tulips",
}
```

### **선택적 속성**

타입의 속성 애너테이션에서 : 앞에 ? 를 추가하면 선택적 속성이 된다.

선택적 속성은 해당 속성을 제공하거나 생략할 수 있다.

```tsx
type Book = { // Book 객체 타입 별칭
	author?: string; // 선택적 속성
	pages: number;
};

const ok: Book = {
	pages: 372,
};

const missing: Book = {
	author: "이대호" 
	// Error: Property 'pages' is missing ..
};
```

## 4.3 객체 타입 유니언

하나 이상의 서로 다른 객체 타입이 될 수 있는 타입을 말한다.

명시적으로 작성할 수 있고, 그렇지 않은 경우는 타입스크립트가 객체 타입 유니언으로 유추할 수 있다.

```tsx
const poem = Math.random() > 0.5 ?
	{ name: "Kim", pages: 7 } : { name: "Park", rhymes: true };

// poem 타입: { name: "Kim", pages: 7 } | { name: "Park", rhymes: true }
```

하나 이상의 서로 다른 객체 타입 중에서 특정 객체 타입으로 내로잉 하는 방법을 객체 타입 내로잉이라고 한다. 

원리는 **객체의 형태를 확인하고 타입 내로잉이 객체에 적용**된다. if(poem.pages)와 같은 형식으로 참 여부를 확인하는 것은 허용하지 않는다.

```tsx
type PoemWithPages = {
	name: string;
	pages: number;
};

type PoemWithRhymes = {
	name: string;
	rhymes: boolean;
};

type Poem = PoemWithPages | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5 ?
	{ name: "Kim", pages: 7 } : { name: "Park", rhymes: true };

poem.name // OK
poem.pages // Error
poem.rhymes // Error

if(pages in poem) {
	poem.pages;
}
else {
	poem.rhymes;
}
```

```tsx
type PoemWithPages = {
	name: string;
	pages: number;
	type: 'pages';
};

type PoemWithRhymes = {
	name: string;
	rhymes: boolean;
	type: 'rhymes';
};

type Poem = PoemWithPages | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5 ?
	{ name: "Kim", pages: 7, type: "pages", } 
: { name: "Park", rhymes: true, type: "rhymes" };

if(poem.type === "pages") { // 객체의 속성이 객체의 형태를 나타내도록 함
	poem.pages;
}
else {
	poem.rhymes;
}
```

## 4.4 교차 타입

교차 타입 & 를 사용해 여러 타입을 동시에 나타낼 수 있다.

교차 타입은 유용하지만 혼동을 일으킬 수 있다. 그러므로 사용해야 한다면 최대한 직관적으로 작성해야 한다.

교차 타입은 불가능한 타입을 생성할 수 있다. 이 때 표현되는 타입이 **never**이다.

```tsx
type NotPossible = number & string; // never
```