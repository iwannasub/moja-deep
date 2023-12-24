# [모자딥 스터디] 14장 ~ 18장

# 14장 - 전역 변수의 문제점

## 호이스팅의 범위

```ts
var x = "global";

function foo() {
  console.log(x);
  var x = "local";
}

foo();
console.log(x);
```

위 코드의 결과를 한 번 예측해보자. 나는 'global', 'global'이라 생각했다.
하지만 결과는 undefined, 'global'이었다. 왜? `함수 내부에서 새로운 x에 대한 선언이 발생을 했고` 함수 내부적으로는 선언된 변수를 호이스팅하여 undefined로 초기화했기 때문이다.

여기서 우리는 런타임 이전에 변수 선언부를 초기화하는 과정인 "호이스팅은 스코프 단위로 이루어진다"를 알 수 있다.

그러면 이제 아래 코드의 결과를 예측해보고, 다음 주제로 넘어가자.

```ts
var x = "global";

function foo() {
  console.log(x);
}

foo();
console.log(x);
```

## 전역 변수는 문제가 있다

- 암묵적 결합: 전역 변수는 코드의 어느 곳에서도 할당하고, 참조할 수 있는 변수이다. 이는 변수의 `유효 범위가 크다`는 것을 의미하고, 이것은 의도치 않게 변수의 상태를 변경하게 만드는 위험성이 있다.
- 생명주기가 길다
- 스코프 체인 상에서 가장 마지막에 검색되기에, 전역 변수의 검색 속도가 가장 느리다.

- 자바스크립트는 파일이 분리되어 있다해도 하나의 전역 스코프를 공유한다. 따라서, 다른 파일 내에서 동일한 이름의 전역 변수, 전역 함수가 존재할 경우 큰 문제가 생길 수 있다. <= var키워드는 변수의 중복 선언을 허용하기에

> var 키워드로 정의된 전역변수는 window 객체에 등록된다.
> ![](https://velog.velcdn.com/images/seungrok-yoon/post/1fd36489-1c71-438e-a192-02079d4ce48f/image.png)

## 변수의 스코프는 좁을 수록 좋다

따라서 전역변수보다는 지역변수를 되도록 사용하자. 이를 위한 방법엔 여러가지가 있다.

- 네임스페이스 객체

```ts
var MYAPP = {}; //전역 네임스페이스 객체

MYAPP.name = "LEE";
console.log(MYAPP.name);
```

- 즉시 실행 함수
  즉시 실행 함수를 통해서 변수를 함수 스코프 -지역 스코프 - 로 바꿔주자. 전역 변수의 사용을 제한할 수 있다.

```ts
(function () {
  var food = 10;
})();

console.log(food);
```

- 모듈 패턴
  즉시 실행 함수의 반환값을 함수 내부 변수값을 참조하는 객체로 만들면, 모듈 패턴을 사용할 수 있다.

`클로저`(24장) 개념을 참고한 이 패턴은, 마치 클래스를 모방한 듯이 관련 있느 변수와 함수를 즉시 실행 함수로 감싸 하나의 모듈을 만드는 패턴이다.

물론 전역 네임스페이스의 오염을 막는 기능은 한정적이지만, 정보 은닉을 구현하기 위해 사용할 수 있다.

```ts
var Counter = (function () {
  var num = 0;
  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
})();

console.log(Counter.num);
console.log(Counter.increase());
console.log(Counter.decrease());
```

- ES6 모듈 사용하기
  var로 정의한 자바스크립트 전역 변수는 window객체의 프로퍼티로 등록된다고 앞에서 설명했었다. 이 문제를 ES6에서는 'module'타입으로 스크립트를 정의하는 방법을 통해 전역변수를 사용할 수 없도록 만들었다.

![](https://velog.velcdn.com/images/seungrok-yoon/post/bae29519-1a7d-415a-b7a8-34c61fadeeaa/image.png)

# 15장 - let, const 키워드와 블록 레벨 스코프

var키워드는

- 변수가 중복 선언이 가능하고
- 스코프가 `힘수레벨 스코프`여서 함수 외부에서는 var 키워드 선언 변수가 코드 브록 내에 중복 선언이 가능한 문제
- undefined로 초기화되는 호이스팅 - 이것은 변수 선언 전에 변수를 참조하는 휴먼 에러를 발생시킬 여지를 남기며, 가독성을 떨어뜨린다.

반면, let 키워드는

- 변수 중복 선언 금지
- 블록 레벨 스코프
- TDZ(Temporal Dead Zone) - ReferenceError를 통해서 호이스팅이 발생하지 않는 `것처럼` 동작한다.

만약 호이스팅이 진짜로 발생하지 않는다면, 아래 코드에서 첫 번째 콘솔문은 무조건 전역 변수를 참조하여 1을 출력할 것이다.

하지만 ReferenceError가 발생하고, 이는 블록 레벨 스코프의 시작부터, 변수의 초기화단계 시작 지점(변수 선언문)까지 변수를 참조할 수 없는 TDZ(Temporal Dead Zone)구간동안에 해당 변수를 참조하여 발생했기 때문이다.

즉, let 변수도 호이스팅은 발생하나 TDZ로 변수의 참조 구간을 제한한 것이다.

```ts
let variableLet = 1;
{
  console.log(variableLet);
  let variableLet = 2;
}

console.log(variableLet);
```

## 전역 객체와 let

```ts
//전역 변수
var x = 1;
//암묵적 전역 - 변수 선언 없이 값을 할당한 변수
y = 2;
//전역 함수
function foo(){
  ...
}

console.log(window.x)
console.log(window.y)
console.log(window.foo)
```

전역 객체 window의 프로퍼티가 된다. 전역 객체의 프로퍼티를 참조할 때는 window를 생략할 수 있다.

하지만 let 변수는 전역 객체의 프로퍼티가 아니다.

## const 키워드

let 과 비슷한 특징을 가지고 있는 const는

- 선언과 초기화를 동시에 해야한다

```ts
let a; //가능
a = 20;

const b; //불가능 SyntaxError: Missing initializer in const declaration 발생
```

- 재할당 금지
- 어디에써? => 상수 관리 할 때 -> 유지보수성이 커진다. -> ESLint 규칙의 [no-magic-numbers](https://eslint.org/docs/latest/rules/no-magic-numbers) 규칙을 보면 이해가 쉽다. 변경이 발생하지 않고, 읽기 전용으로 사용하는 원시 값과 객체에는 const 키워드를 사용하자.

## const 는 재할당이 금지인 것이지, 불변을 의미하지는 않는다.

const 키워드로 선언된 객체를 변경시키더라도, 변수에 할당된 `참조 값`은 변경되지 않는다.

```ts
const person = { name: "Seungrok" };
person.name = "Yuri";
```

# 16장 - 프로퍼티 어트리뷰트

> 내부 슬롯(internal slot) 내부 메서드(internal method)의 개념에 대해 알아보자. 이 장을 공부하면서 Object.create 내장 매서드를 사용하면서 들었던 의문들이 조금씩 해소가 되는 느낌이었습니다.

내부 슬롯, 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 구현하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티(pseudo property)와 의사 메서드(pseudo method)이다. 이중 대괄호로 감싼 프로퍼티 이름들이 내부 슬롯과 내부 메서드이다.

기본적으로는 사용자가 접근할 수 없지만, 일부에 한하여 간접적으로 접근할 수 있는 수단을 제공한다.

```ts
const o= {}
o.[[Prototype]] //불가
o.__propto__ //가능
```

## 프로퍼티 어트리뷰터, 프로퍼티 디스크립터 객체

> 자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의한다.

## 데이터 프로퍼티와 접근자 프로퍼티

사실 객체의 프로퍼티는

- 데이터 프로퍼티: 키와 값으로 구성된 일반적인 프로퍼티
- 접근자 프로퍼티: 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티

이렇게 두 종류로 구분할 수 있다.

### 데이터프로퍼티

아래와 같은 프로퍼티 어트리뷰트를 갖는다.

프로퍼티 어트리뷰트는 자바스크립트 엔진이 관리하는 내부 상태값(meta-property)인 내부 슬롯 [[Value]],[[Writable]],[[Enumerable]],[[Configurable]]이다. 이 내부 슬롯은 직접 접근할 수는 없지만. `Object.getOwnPropertyDescriptor`메서드를 사용하여 간접적으로 확인할 수 있다. 이 때 반환되는 프로퍼티 디스크립터 객체의 프로퍼티이름은 value, writable, enumerable, configurable이다.

![](https://velog.velcdn.com/images/seungrok-yoon/post/b165ccb7-be52-480a-911d-760ca6427beb/image.png)

각 프로퍼티 어트리뷰트에 대해 알아보자.

- 값 : value, 프로퍼티 키를 통해 값에 접근하면 반환되는 값. 프로퍼티 키를 통해 값을 저장하면 [[Value]]에 값이 저장됨. 만약 객체에 없던 프로퍼티라면, 동적으로 프로퍼티를 생성하고 여기에 값을 저장.

- 갱신 가능 여부 : writable, 이 값이 false라면, [[Value]]의 값을 변경할 수 없는 읽기 전용 프로퍼티가 된다.
- 열거 가능 여부 : enumerable, true인 경우에는 for...in 또는, Object.keys메서드로 열겨할 수 있다
- 재정의 가능 여부 : configurable, false의 경우에는 프로퍼티 값의 삭제, 프로퍼티 어트리뷰트의 값 변경이 금지된다.

### 접근자 프로퍼티

아래와 같은 프로퍼티 어트리뷰트를 갖는다.

- [[Get]] : getter, 접근자 프로퍼티를 통해 `데이터 프로퍼티의 값을 읽을 때` 호출되는 접근자 함수이다.
- [[Set]] : setter, 접근자 프로퍼티를 통해 `데이터 프로퍼티의 값을 저장할 때` 호출되는 접근자 함수이다. 접근자 프로퍼티 키로 프로퍼티 값을저장하면, 프로퍼티 어트리뷰트 [[Set]]이 실행되고 그 결과가 최종 값으로 저장이 된다. -[[Enumerable]] : 데이터프로퍼티의 [[Enumerable]]과 동일 -[[Configurable]]: 데이터 프로퍼티의 [[Conficurable]]과 동일.

```ts
const animal = {
  name: "Kookoo",
  species: "bird",
  get fullAcademicName() {
    return `${this.name} ${this.species}`;
  },
  set fullAcademicName(name) {
    [this.name, this.species] = name.split(" ");
  },
};

console.log(animal.fullAcademicName);
animal.fullAcademicName = "seungrok human";
console.log(animal.fullAcademicName);
```

## 프로퍼티 정의

새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의하는 것을 말한다.

```ts
const me = {};
Object.defineProperty(me, "firstName", {
  value: "Seungrok",
  writable: false,
  enumerable: true,
  configurable: false,
});

console.log(me);
```

## 객체 변경 방지

- 객체 확장 금지 : Object.preventExtensions - 프로퍼티 추가만 안 됨
- 객체 밀봉 : Object.seal - 프로퍼티 값 읽고 쓰기만 가능
- 객체 동결: Object.freeze - 읽기만 가능해짐

# 17장 - 생성자 함수에 의한 객체 생성

자사스크립트는 Object생성자 함수 이외에도 String, Number, Boolean, Function, Array 등의 빌트인 생성자 함수를 제공한다.

> 근데 리터럴보다 불편한 것 같은데 왜 사용하지?

### 객체 리터럴에 의한 객체 생성 방식의 문제점

객체 리터럴은 단 하나의 객체만 생성하기에, 동일한 프로퍼티를 갖는 객체를 여러개 생성해야 하는 경우 비효율적이다.

반면, 생성자 함수로 객체를 생성하면 구조가 동일한 여러 객체를 간편히 생성할 수 있다.

```ts
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.getInfo = function () {
    return { name: this.name, age: this.age };
  };
}

const aPerson = new Person("Seungrok", 31);
console.log(aPerson.getInfo());
```

### 생성자 함수 인스턴스 생성 과정

> 인스턴스 생성(필수) + 생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당 - 옵션)

위 예시에서는 어디에서도 생성된 인스턴스를 반환하는 코드가 보이지 않는다. 그 이유는 `자바스크립트 엔진은 new 연산자와 함께 생성자 함수가 호출될 경우, 암묵적으로 인스턴스를 생성하고 반환한다.`

### 인스턴스 생성과 this 바인딩

객체 인스턴스에 this가 바인딩된다. 바인딩은 '연결'이라고 생각하면 이해가 편하다. 식별자와, 실제 객체를 이어주는 작업이다.

this 바인딩은 런타임 이전에 실행된다.

### this 반환

생성자 내부의 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환이 된다. 만약 함수가 다른 객체를 명시적으로 반환하면 this를 반환하지 않고, 그 객체를 반환한다.

```ts
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.getInfo = function () {
    return { name: this.name, age: this.age };
  };
  return {};
}

const aPerson = new Person("Seungrok", 31);
console.log(aPerson);
```

## 그러면 일반 객체랑 같아?

함수는 호출할 수 있기에, 내부적으로는 [[Call]], [[Construct]] 내부 메서드와 [[Environment]], [[FormalParameters]]를 추가로 가지고 있다.

일반함수로 호출될 때는 [[Call]](callable)이, 생성자함수로 호출될 때는 [[Construct]](constructor)가 호출이 된다.

### 생성자 함수로 호출되었는지를 판단하고, 일반함수 호출을방지하는 방법

```ts
function Circle(radius) {
  if (!new.target) {
    return new Circle(radius);
  }
  if (!(this instanceof Circle)) {
    return new Circle(radius);
  }
}
```

# 18장 - 함수와 일급 객체

자바스크립트에서 함수는 일급 객체이다. 일급 객체는 값으로서 사용이 될 수 있는데, 이 말은

- 무명의 리터럴로 생성가능하고(런타임에 생성이 가능하고)
- 변수나 자료구조에 저장할 수 있으며
- 함수의 매개변수에 전달할 수도 있고
- 함수의 반환값으로도 사용할 수 있다.

그러나 앞장에서도 설명했듯이, 기본적으로 callable이기에, 일반 객체와 대비되는 고유의 프로퍼티가 존재한다.

- arguments - 인수들을 담고 있는 유사 배열 객체
- length - 매개변수의 개수
- name - 함수 객체를 가리키는 식별자
- **proto**

##

# 실습

1. 다음 조건의 전역 객체를 생성해보라.
   변수명: student

- 데이터 프로퍼티: 키: firstName, 값: 문자열
- 데이터 프로퍼티: 키: lastName, 값: 문자열
- 접근자 프로퍼티 키: fullName, 값: 문자열

접근자 프로퍼티에 새로운 이름을 할당할 때는, 새로운 이름을 공백 기준으로 split하여 앞을 firstName으로, 뒤 요소를 lastName으로 설정한다.

2. uniquePerson이라는 변수명을 지닌 빈 객체가 하나 존재한다.
   여기에 'firstName' 데이터 프로퍼티를 추가한다. 단, 이 프로퍼티의 값은 수정 불가능하도록, 그리고 삭제되지도 않게 정의해보자.

이후 추가로, uniquePerson 객체는 동결하여 등록된 프로퍼티값들을 읽을수만 있게 만들자.

- 힌트: Object.defineProperty 를 활용할 것

3. 다음 코드의 출력내용을 예상하여 설명해보라.

```ts
const me = {};
Object.defineProperty(me, "firstName", {
  value: "Seungrok",
  writable: false,
  enumerable: true,
  configurable: false,
});

console.log(me);
delete me.firstName;
console.log(me);
// me.firstName = "Dan";

Object.defineProperty(me, "lastName", {
  value: "Yoon",
  writable: true,
  enumerable: false,
  configurable: true,
});

me.lastName = "Park";
console.log(Object.getOwnPropertyDescriptors(me));
delete me.lastName;
console.log(Object.getOwnPropertyDescriptors(me));
```
