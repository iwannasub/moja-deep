# [모자딥 스터디] 22장 ~ 25장

# 22장 - this

## this 는 식별자이다

```ts
//(1)
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * circle.radius
  },
}

circle.radius = 10
console.log(circle.getDiameter())

//(2)
function Circle(radius) {
  ????.radius = radius // this.radius
}

const a = new Circle(5)
const b = new Circle(10)
```

(1) 객체 리터럴 방식으로 객체를 생성한 경우, 매서드 내부에서 자신의 객체를 가리키는 식별자를 `재귀적으로 참조`할 수 있다.circle이라는 식별자에 객체 리터럴이 할당되었을 당시에는 객체 리터럴의 평가가 완료된 상태이기 때문이다. circle 식별자에 객체 리터럴이 할당되었다는 것은 할당문의 평가가 완료되었음을 의미한다. circle.getDiameter()를 호출하는 시점에서 이미 circle이 어떤 객체를 참조하는지가 정해진 셈이다. getDiameter내부에서 circle에 대한 참조값을 얻을 수 있기 때문에 재귀적 참조가 가능했던 것이다.

(2)그렇지만, 생성자 함수를 통한 객체 생성 시에는 이런 재귀적 참조가 불가능하다. 생성자 함수는 new 연산자를 통해서 객체 인스턴스를 생성한다. 즉, 인스턴스가 생성되어야 객체가 생기는 것인데, `어떻게 생성자 함수의 정의 단계에서 미래의 인스턴스를 가리킬 수 있을까?`

`this`키워드를 통해 이 작업이 가능하다.

함수를 호출하면 arguments객체와 this가 암묵적으로 함수 내부에 전달이 된다. 그리고 함수가 `호출되면` -> `호출 방식에 따라` this 바인딩, 즉 this라는 식별자에 어떤 객체의 참조값이 전달될 것인지가 동적으로 결정된다.

그러면 함수의 호출 방식에 따라 this가 어떻게 결정되는지 하나씩 알아보자.

## 함수의 호출 방식에 따라 달라지는 this

### 1. 메서드로 함수가 호출될 때

이 경우는 메서드를 호출한 객체를 가리킨다.

```ts
const obj = {
  son: 100,
  foo() {
    console.log(this.son);
  },
};
obj.foo();
```

```ts
const obj2 = {
  son: 100,
  foo() {
    console.log(this.son);
    function innerFunction() {
      console.log(this.son);
    }
    innerFunction();
  },
};
obj2.foo();
```

### 2. 일반함수로 호출될 때

일반 함수로 호출될 때는 전역 객체가 this에 바인딩된다.

```ts
function normalFunction() {
  console.log(this);
}

const anObj = { normalFunction };

normalFunction(); // (1) 일반함수로 호출되었을 때
anObj.normalFunction(); // (2)객체의 메서드로서 호출되었을 때
```

비록 normalFunction은 일반함수로 정의되었지만, `호출방식`이 일반함수일 때와 메서드일 때 this가 다르게 출력된다.

중첩함수와 콜백함수도 일반함수로 호출이 된다면 this가 전역 객체를 가리킨다. 즉 일반함수로 호출될 때는 this는전역객체와 바인딩된다.

```ts
const obj2 = {
  son: 100,
  foo() {
    console.log(this.son);
    function innerFunction() {
      console.log(this.son);
    }
    innerFunction();
  },
  bar() {
    setTimeout(function () {
      console.log("Callback`s this", this);
      console.log("Callback`s son", this.son);
    }, 100);
  },
};
obj2.foo();
obj2.bar();
```

하지만, 메서드 내부에서 정의된 일반함수의 this가 메서드 함수의 this와 일치하지 않는 것은 부자연스러운 것이 사실이다. this를 일치시켜주기 위해 우리는 아래와 같은 방법으로 일반함수의 this를 메서드가 가리키는 객체로 바인딩을 변경해줄 수 있다.

```ts
const obj2 = {
  son: 100,
  foo() {
    console.log(this.son);
    const that = this;
    function innerFunction() {
      console.log(this.son);
    }
    innerFunction.call(this);
  },
  bar() {
    setTimeout(function () {
      console.log("Callback`s this", this);
      console.log("Callback`s son", this.son);
    }, 100);
  },
};
obj2.foo();
obj2.bar();
```

### 궁금증: 메서드는 객체에 종속적인가?

일반함수를 객체에 등록해주고 호출하면 어떻게 될까?

```ts
function printName() {
  console.log(this.name);
}

const obj1 = { name: "seungrok", printName };
obj1.printName();
const obj2 = { name: "soojin" };
obj2.printName = obj1.printName;
obj2.printName();
```

위 코드의 출력값은 'seungrok'과 'soojin'이다. 메서드가 객체에 포함된 것(가리키는 객체가 항상 동일)이라면, obj2.printName()은 'seungrok'을 출력했어야 하겠지만, 그렇지 않다.

이는 곧 `프로퍼티가 단순히 함수 객체를 가리키고 있을 뿐`임을 시사한다. 따라서 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와 관련이 없고, 메서드를 호출한 객체에 바인딩된다.

이는 프로토타입 메서드 내부의 this에도 동일하게 적용된다.

## 3. 생성자 함수로 호출될 때

함수가 생성자 함수로 호출이 되는 경우, this는 생성자 함수가(미래에)생성할 인스턴스가 바인딩된다.
![](https://velog.velcdn.com/images/seungrok-yoon/post/c18e2c85-fbc2-46e3-9ea9-d5d0584f541d/image.png)

```ts
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Lee");
console.log(me.getName());

Person.prototype.name = "Kim";
console.log(Person.prototype.getName());
```

# 23장 - 실행 컨텍스트

## 실행 컨텍스트

실행컨텍스트는 소스코드를 실행하는데 필요한 환경을 제공하고, 코드의 실행 결과를 실제로 관리하는 영역이다. => 식별자를 등록하고 관리하는 스코프, 코드 실행 순서관리 이 두 가지를 구현한 내부 메커니즘이다.

## 코드에 따라 실행 컨텍스트 생성 과정과 내용이 다르다

소스코드는 크게 4가지 타입으로 구분된다

- [x] 전역코드
- [x] 함수코드
- eval코드
- 모듈코드

## 실행 컨텍스트의 구성

- 식별자와 스코프를 등록하고 관리, 상위 스코프에 대한 참조를 기록 => 렉시컬 환경이라 불리는 자료구조의 역할
- 코드의 실행 결과를 관리 - 코드의 실행순서를 관리 => 실행 컨텍스트 스택의 역할

즉 실행 컨텍스트는 `렉시컬 환경` + `실행 컨텍스트 스택`으로 이루어져 있다.

## 렉시컬 환경

아래 코드가 실행되는 과정을 보며 실행 컨텍스트 생성 과정을 자세히 살펴보자.

```ts
var x = 1;
const y = 2;
function foo(a) {
  var x = 3;
  const y = 4;
  function bar(b) {
    const z = 5;
    console.log(a + b + x + y + z);
  }
  bar(10);
}
foo(20); //42
```

### 전역 실행 컨텍스트 생성

(1)
전역 객체 생성(전역 코드가 평가되기 이전에 생성) -> 실행 컨텍스트 스택에 푸시

소스코드 로드

전역 소스코드의 평가

page371도식 참고
전역 코드 평가 과정에서는

- 전역 실행 컨텍스트 생성
- 전역 실행 컨텍스트가 참조할 `전역 렉시컬 환경` 생성
  - 객체 환경 레코드 생성: var, 함수 선언 등록 -> 전역 객체에 등록, 빌트인 전역 함수, 표준 빌트인 객체 관리하는 레코드
    - 선언적 환경 레코드 생성: let, const로 선언된 전역 변수 관리, 등록되는 별도의 공간
- this 바인딩
  전역 환경 레코드의 [[GlobalThisValue]]내부 슬롯에 this가 바인딩됨. 전역 코드에서 this는 전역 객체기에, 전역 객체가 등록됨
- 외부 렉시컬 환경에 대한 참조 결정 -> null

(2) foo 함수 호출
호출되면 평가하고, 실행한다.
함수 코드 평가는 다음과 같은 순서로 진행된다.

함수 실행컨텍스트 생성 -> 실행 컨섹스트 스택에 푸시된다.
함수 렉시컬 환경 생성

- 함수 환경 레코드 생성
- this 바인딩
  함수 호출방식에 따라 함수환경레코드의 [[ThisValue]] 내부슬롯에 this가 바인딩된다.
- 외부 렉시컬 환경에 대한 참조 결정
  > 함수는 어디서 정의되었는지에 따라 상위 스코프를 결정한다. 함수 정의를 평가하여 함수 객체를 생성할 때, 현재 실행중인 실행 컨텍스트의 렉시컬 환경인 함수의 상위 스코프를 함수 객체의 내부 슬롯 [[Environment]]에 저장한다.

함수의 OuterLexicalEnvironment Reference에는 바로 이 [[Environment]]참조값이 할당된다. 이 말은 즉, 함수의 상위 스코프는 함수를 정의했던 실행 컨텍스트의 렉시컬 환경인 것이다.다시 말해 `스코프는 실행 컨텍스트의 렉시컬 환경`이다!

(3)bar 함수 호출
동일하다.
다만, bar함수 실행 컨텍스트의 렉시컬 환경의 OuterEnvironmentRecord는 foo함수의 실행 컨텍스트가 될 것이다. 왜? bar함수가 평가될 당시의 실행중인 실행 컨텍스트는 foo였으니까!

## 실행 컨텍스트와 블록 레벨 스코프

p387참조

> 코드 블록이 실행되면 해당 코드 블록을 위한 블록 레벨 스코프가 매번 새로 생기고, 외부 렉시컬 환경에 대한 참조는 해당 블록문이 실행되기 전의 렉시컬 환경을 가리킨다.

```ts
let x = 1;
if (x === 1) {
  let x = 10;
  console.log(x);
}
console.log(x);
```

## 퀴즈

퀴즈 1

```ts
//22장 - 퀴즈 1
function Person(name) {
  this.name = name;
  this.getName = () => {
    return "Method in Constructor Function " + this.name;
  };
}

Person.prototype.getName = function () {
  return "Method in Prototype " + this.name;
};

const me = new Person("Lee");
console.log(me.getName());

Person.prototype.name = "Kim";
console.log(Person.prototype.getName());
```

퀴즈 2

```ts
//22장 퀴즈 2
function Person(name) {
  this.name = this.__proto__.name || name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Lee");
console.log(me.getName());

Person.prototype.name = "Kim";
console.log(Person.prototype.getName());

const anotherMe = new Person();
console.log(anotherMe.getName());
```

퀴즈 3: 렉시컬 환경은 외부 렉시컬 환경에 대한 참조(OuterLexicalEnvironmentReference)를 통해 상위 스코프(상위 렉시컬 환경)을 기억한다. 렉시컬 환경의 '외부 렉시컬 환경에 대한 참조'의 연쇄는 단방향 링크드 리스트 형식의 `스코프체인`을 구성하게 된다.

주어진 조건에 따라 클래스 문법을 활용하여 렉시컬 환경들로 이루어진 단방향 링크드 리스트(LinkedList) 클래스와 렉시컬 환경 클래스(LexicalEnvironment)클래스를 구현해보시오.

```ts
class LexicalEnvironment {
  constructor() {
    this.EnvironmentRecord = null;
    this.OuterLexicalEnvironmentReference = null;
  }
}

class LinkedList {
  constructor() {
    this.executingLexicalEnv = null;
  }
  findProperty() {}
  getNextLexicalEnvironment() {}
  pushLexicalEnvironment() {}
}

//아래 배열은 각 링크드 리스트의 EnvironmentRecord에 등록될 객체정보이다.
//먼저 나온객체 순서대로 실행컨텍스트를 생성하자. 앞의 객체는 뒤의 객체의 상위 렉시컬 환경임이 보장되어 있다.
const objs = [
  { a: 1, b: 2 },
  { c: 1, d: 2 },
  { e: 1, f: 2 },
  { g: 1, h: 2 },
];
```

조건1: 각 렉시컬 환경(LexicalEnvironment)클래스는 프로퍼티로 EnvironmentRecord, OuterLexicalEnvironmentReference를 지니고 있다. 생성자 함수는 EnvironmentRecord에 등록될 객체를 받는다.

조건2: 스코프체인은 렉시컬 환경의 OuterLexicalEnvironmentReference가 다음 렉시컬 환경 객체를 참조함으로써 구성된다.

조건3: 구현된 단방향 링크드 리스트 클래스는 프로퍼티로 현재 실행중인 실행 컨텍스트의 렉시컬 환경인 executingLexicalEnv를, 메서드로는 해당 렉시컬 환경의 EnvironmentRecord에서 프로퍼티를 검색하는 findProperty메서드와, 상위 스코프로 현재 바라보고 있는 렉시컬 환경을 변경해주는 getNextLexicalEnvironment메서드, 그리고 새로운 렉시컬 환경을 리스트의 마지막에 등록해주는 push 메서드가 존재한다.

findProperty메서드는 검색하고자하는 프로퍼티가 존재할 때는 해당 프로퍼티 값을 리턴하고, 그렇지 않다면 'RerefenceError'를 발생시키시오

```ts
class LexicalEnvironment {
  //node
  constructor(obj) {
    this.EnvironmentRecord = obj;
    this.OuterLexicalEnvironmentReference = null;
  }
}

class LinkedList {
  //node들의 단방향 연결
  constructor() {
    this.executingLexicalEnv = null;
  }

  findProperty(str) {=
    let currEnv = this.executingLexicalEnv;
    let found = false;
    while (!found) {
      if (currEnv.EnvironmentRecord[str]) {
        found = true;
        continue;
      }
      if (currEnv.OuterLexicalEnvironmentReference === null) break;
      currEnv = currEnv.OuterLexicalEnvironmentReference;
    }
    if (found) return currEnv.EnvironmentRecord[str];
    throw new Error("ReferenceError");
  }
  getNextLexicalEnvironment() {
    if (this.executingLexicalEnv === null)
      throw new Error("No executingLexicalEnv");
    return this.executingLexicalEnv.OuterLexicalEnvironmentReference;
  }
  pushLexicalEnvironment(lexicalEnvironment) {
    lexicalEnvironment.OuterLexicalEnvironmentReference =
      this.executingLexicalEnv;
    this.executingLexicalEnv = lexicalEnvironment;
  }
}

// LinkedList
// .executingLexicalEnv -> LinkedList
//                         .executingLexicalEnv ->  LinkedList
//                                                         .executingLexicalEnv -> LinkedList LinkedList

//아래 배열은 각 링크드 리스트의 EnvironmentRecord에 등록될 객체정보이다.
//먼저 나온객체 순서대로 실행컨텍스트를 생성하자. 앞의 객체는 뒤의 객체의 상위 렉시컬 환경임이 보장되어 있다.
const objs = [
  { a: 1, b: 2 },
  { c: 1, d: 2 },
  { e: 1, f: 2 },
  { g: 1, h: 2 },
];

const linkedList = new LinkedList();
objs.forEach((v) =>
  linkedList.pushLexicalEnvironment(new LexicalEnvironment(v))
);

console.log(linkedList.findProperty("a"));

```
