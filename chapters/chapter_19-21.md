# 이선근 3주차 (19, 20, 21 장)

# 19. 프로토타입

자바스크립트는 명령형, 함수형, 프로토타입 기반 객체 지향 프로그래밍을 지원하는 멀티 패러다임 프로그래밍 언어다.

## 객체지향 프로그래밍

객체지향 프로그래밍은 전통적인 절차지향적 관점에서 벗어나, 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임이다.

객체는 상태를 나타내는 **프로퍼티**, 동작을 나타내는 **메서드**를 묶은 복합적인 자료구조이다.

각 객체는 독립적인 프로퍼티와 메서드를 가질 수 있고, 다른 객체의 것을 상속받아 사용할 수도 있다.

## 상속과 프로토타입

상속: 어떤 객체가 다른 객체의 프로퍼티, 메서드를 그대로 사용할 수 있는 것으로, 중복을 제거하고 기존의 코드를 재사용한다.

자바스크립트는 프로토타입을 기반으로 상속을 구현해 불필요한 중복을 제거한다.

```jsx
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    return Math.PI * this.radius ** 2;
  };
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea); // false
```

이렇게 만들면 인스턴스를 생성할 때마다 getArea 함수를 중복 생성해 메모리가 낭비된다.

```jsx
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea); // true
```

모든 인스턴스가 동일하게 사용하는 메서드는 프로토타입에 추가해서 인스턴스가 상속 받아서 사용하게 만들자

## 프로토타입 객체

프로토타입은 어떤 객체의 상위 객체의 역할을 하는 객체로, 다른 객체에 공유 프로퍼티를 제공한다. 프로토타입을 상속받은 자식은 부모의 프로퍼티를 자신의 프로퍼티처럼 사용할 수 있다.

모든 객체는 [[prototype]]이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fe123c6ea-1a27-421a-910c-fb5b50c56d34%2FUntitled.png?table=block&id=242c0b30-bd58-4c60-81c6-af3752ab5de8&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1150&userId=&cache=v2)

[[Prototype]]에 직접 접근하지 못하지만 간접적으로 접근할 수 있다.

- 생성자함수.prototype
- 인스턴스.\_\_proto__

### 접근자 프로퍼티 \_\_proto__

접근자 프로퍼티는 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수, [[Get]], [[Set]] 프로퍼티로 구성되어있다.

이를 통해 프로토타입을 취득하거나 할당한다.

접근하면 getter 함수인 [[Get]]실행

할당하면 setter 함수인 [[Set]] 실행

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F41e9d1f6-7e8b-4efe-8433-2d02e4551353%2FUntitled.png?table=block&id=c14965fa-91ba-46b9-a08c-721802b03c59&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=670&userId=&cache=v2)

접근자 프로퍼티는 객체가 직접 소유한 것이 아니라 Object.prototype의 프로퍼티다.

이렇게 프로토타입에 접근하는 이유는?

상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위함이다.

프로토타입 체인은 단방향으로 흘러가야하는데, 순환 참조하는 프로토타입 체인이 만들어지면 프로토타입 체인에서 프로퍼티를 검색할 때 무한루프에 빠진다.

따라서 프로토타입을 직접 교체할 수 없도록 접근자 프로퍼티를 통해 교체하도록 구현되어있다.

<aside>

[Object.prototype.\_\_proto__ - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)

`Object.getPrototypeOf`, `Object.setPrototypeOf`를 사용해 접근, 교체 권장

</aside>

## 함수 객체의 프로토타입

일반 객체에는 없는 함수 객체의 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.

생성자 함수로 호출할 수 없는 함수를 non-constructor라고 하는데 화살표 함수와 es6 메서드 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 생성하지 않는다.

모든 객체가 가지는 \_\_proto__와 함수 객체만 가지는 prototype은 동일하지만 사용 주체가 다르다.

- \_\_proto__ 접근자 프로퍼티 : 객체가 자신의 프로토타입에 접근, 교체하기 위해 사용
- prototype 프로퍼티 : 생성자 함수가 생성할 인스턴스의 프로토타입을 할당하기 위해 사용

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fa4d9cdd0-6afe-4aeb-aca8-ace60608769c%2FUntitled.png?table=block&id=2671a922-2553-4ad8-84dd-193e0a07a11f&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1500&userId=&cache=v2)

프로토타입의 constructor 프로퍼티는 자신을 참조하고있는 생성자 함수를 가리킨다. 함수 객체가 생성될 때 연결이 이루어짐.

## 리터럴 표기법

추상 연산 OrdinaryObjectCreate를 호출해 Object.prototype을 프로토타입으로 갖는 빈 객체를 생성한 후 프로퍼티를 추가한다.

리터럴 표기법으로 객체를 생성해도 생성자 함수를 갖는다. 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 항상 쌍으로 존재한다.

## 프로토타입 생성 시점

프로토타입은 생성자 함수가 생성되는 시점에 생성된다.

- 사용자 정의 생성자 함수
  - 함수 정의(constructor)가 평가돼 함수 객체를 생성하는 시점에 프로토타입 생성,
    생성자 함수 prototype 프로퍼티에 바인딩
  - non-constructor는 프로토타입 생성 X
  - 생성된 프로토타입의 프로토타입은 언제나 Object.prototype이다.
- 빌트인 생성자 함수
  - 빌트인 생성자 함수가 생성되는 시점(전역 객체 생성 시점)에 생성

## 객체 생성 방식과 프로토타입의 결정

프로토타입은 추상 연산 OrdinaryObjectCreate에 전달되는 인수에 의해 결정된다. 인수는 객체 생성 시점에 객체 생성 방식에 의해 결정된다.

- 객체 리터럴
  - 추상 연산 호출, 전달 프로토타입 Object.prototype
- Object 생성자 함수
  - 추상 연산 호출, 전달 프로토타입 Object.prototype
- 생성자 함수
  - 추상 연산 호출, 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩 되어있는 객체다.
    ![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fc2ffb550-4c5f-4226-892a-08a23d4faa38%2FUntitled.png?table=block&id=5cbea5a6-9779-4c13-93b1-ddb064998846&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1150&userId=&cache=v2)

## 프로토타입 체인

객체의 프로퍼티나 메서드에 접근하려고 할 때, 해당 객체에 없다면 [[Prototype]] 내부 슬롯의 참조를 따라 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.

프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 매커니즘이다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F561e98a3-c1aa-469f-bbcc-a3e06cc3d297%2FUntitled.png?table=block&id=d4ea1ec7-2156-4b4d-ab46-cf784a45c6de&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1180&userId=&cache=v2)

Object.prototype은 프로토타입 체인에서 항상 최상위에 있기 때문에 프로토타입 체인의 종점이라고 한다.

### 오버라이딩

상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F8c121acd-2980-41d4-a9f4-c3cee4765cda%2FUntitled.png?table=block&id=b923dce3-fdc1-4c67-b70a-4353ed3d4879&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1280&userId=&cache=v2)

```jsx
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  // 프토토타입 메서드
  console.log(`Hi, My name is ${this.name}`);
};

const me = new Person('Lee');

me.sayHello(); // Hi, My name is Lee

me.sayHello = function () {
  // 인스턴스 메서드
  console.log(`Hello, My name is ${this.name}`);
};

me.sayHello(); // Hello, My name is Lee

delete me.sayHello;
me.sayHello(); // Hi, My name is Lee

delete me.sayHello;
me.sayHello(); // Hi, My name is Lee

delete me.__proto__.sayHello;
console.log(me.sayHello); // undefined
```

하위 객체를 통해 프로토타입에 get은 되지만 set은 안 된다. 따라서 프로토타입 프로퍼티를 변경하려면 프로토타입에 직접 접근해야한다.

## 프로토타입의 교체

생성자 함수와 인스턴스에서 교체할 수 있다.

인스턴스에서 교체하면 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리키지 않는다.

## 직접 상속

Object.create 메서드는 명시적으로 프로토타입을 지정해 새로운 객체를 생성한다.

`Object.create(프로토타입, 프로퍼티 객체)`

new 연산자 없이 객체 생성 가능

- Object.create
- 리터럴 내부에서 \_\_proto__를 사용

## 정적 프로퍼티, 메서드

정적 프로퍼티는 인스턴스 생성 없이 참조할 수 있는 프로퍼티를 말한다.

정적 프로퍼티는 프로토타입 체인에 속한 객체의 프로퍼티가 아니므로 인스턴스에서는 접근 불가

```jsx
function Person(name) {
  this.name = name;
}

Person.sayHi = function () {
  console.log('hi');
};

const me = new Person('Lee');

Person.sayHi(); // hi
me.sayHi(); // Error
```

인스턴스, 프로토타입 메서드 내에서 this를 사용하지 않는다면 정적 메서드로 변경할 수 있다.

## 프로퍼티 열거

프로퍼티 어트리뷰트 [[Enumerable]]의 값이 으로 열거 가능 여부를 갖는다.

for…in 문은 객체의 프로토타입 체인 상에 존재하는 모든 열거 가능한 프로퍼티를 순회한다.

---

# 20. strict mode

```jsx
function foo() {
  aaa = 100;
}
foo();

console.log(aaa);
```

선언하지 않은 변수에 값을 할당해 에러가 발생할 것 같지만 암묵적으로 전역 객체에 aaa 프로퍼티를 동적 생성한다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fe8fd9d2c-6942-4e44-ab30-295ceff2c894%2FUntitled.png?table=block&id=8f3a8a5a-e8b4-4600-8109-e0840ee5b5f3&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=450&userId=&cache=v2)

전역 객체의 프로퍼티는 전역 변수처럼 사용할 수 있고, 이러한 현상을 **암묵적 전역**이라고 한다.

ES5부터 추가된 strict mode는 자바스크립트의 문법을 더 엄격히 적용해 오류를 발생시킬 수 있거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적으로 에러를 발생시킨다.

**전역의 선두** 또는 **함수 몸체의 선두**에 `'use strict'`를 추가하여 strict mode를 적용한다.

## strict mode가 발생시키는 에러

1. 암묵적 전역
2. 변수, 함수, 매개변수의 삭제

   ```
   (function () {
     'use strict';
     var x = 1;
     delete x; // SyntaxError: Delete of an unqualified identifier in strict mode.
   })();
   ```

3. 매개변수 이름의 중복

   ```
   (function () {
     'use strict';

     function foo(x, x) {
       return x + x;
     }

     console.log(foo(1, 2)); // SyntaxError: Duplicate parameter name not allowed in this context
   })();
   ```

   strict mode가 아니라면 x는 2로 할당되어 4가 출력됨

4. with 문의 사용

   ```
   (function () {
     'use strict';

     with ({ x: 1 }) {
       console.log(x); // SyntaxError: Strict mode code may not include a with statement
     }
   })();
   ```

   with문: 전달된 객체를 스코프 체인에 추가

   성능과 가독성이 나빠서 사용하지 않는 것이 좋다.

## strict mode 적용에 의한 변화

1. 일반 함수의 this

   ```
   (function () {
     'use strict';

     function foo() {
       console.log(this); // undefined
     }
     foo();
   })();
   ```

   생성자 함수가 아닌 일반 함수에서 this는 undefined에 바인딩 된다.

2. arguments 객체

   ```
   (function (a) {
     'use strict';

     a = 2;
     console.log(arguments); // [1]
   })(1);
   ```

---

# 21. 빌트인 객체

- 표준 빌트인 객체
  - ECMAScript 사양에 정의된 객체로 실행 환경과 상관없이 언제나 사용할 수 있다.
- 호스트 객체
  - 실행 환경에서 추가로 제공하는 객체. 브라우저에서 DOM, Canvas 등
- 사용자 정의 객체
  - 사용자가 직접 정의한 객체

표준 빌트인 객체는 몇 개를 제외하고 인스턴스를 생성할 수 있는 생성자 함수 객체다.

## 표준 빌트인 객체

- 생성자 함수 객체가 아닌 표준 빌트인 객체
  - Math, Reflect, JSON
  - 정적 메서드만 제공
- 생성자 함수 객체인 표준 빌트인 객체
  - Object, String, Array 등 위 3개를 제외한 40여 개
  - 프로토타입 메서드와 정적 메서드 제공

## 래퍼 객체

```jsx
let str = 'abcde';

console.log(str.length); // 5
console.log(str.toUpperCase()); // ABCDE
```

원시값이 객체처럼 동작하는 이유? 래퍼객체 때문이다

객체처럼 점 표기법으로 접근하면 연관된 객체로 변환 후 프로퍼티에 접근하거나 메서드를 실행한다. 그리고 다시 원시값으로 되돌린다.

과정

1. 원시 값에 점 표기법으로 접근 시, String 생성자 함수의 인스턴스가 생성되고 원시 값인 ‘abcde’는 [[StringData]] 내부 슬롯에 할당
2. 래퍼 객체는 String 프로토타입 메서드 상속받아 사용 가능

   ![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fd952435a-0352-4bd1-9966-9c0c15baff62%2FUntitled.png?table=block&id=194d58e1-818b-475e-b665-6cdb8448ca2b&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=510&userId=&cache=v2)

3. 래퍼 객체의 처리가 끝나면 내부 슬롯에 할당된 원시값 되돌리고 래퍼 객체는 가비지 컬렉션의 대상이 된다.

## 전역 객체

코드가 실행되기 전 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체다.

**특징**

- 개발자가 의도적으로 생성 불가
- 전역 객체의 프로퍼티를 참조할 때 전역 객체 이름(window, global)을 생략할 수 있다.
- 모든 표준 빌트인 객체를 프로퍼티로 가지고있다.
  ```jsx
  console.log(globalThis.Math === Math); // true
  console.log(globalThis.fetch === fetch); // true
  ```
- 실행 환경에 따라 추가적으로 프로퍼티, 메서드를 가진다. (DOM, BOM, Canvas 등)
- var 키워드로 선언한 전역 변수와 암묵적 전역, 전역 함수는 전역 객체의 프로퍼티가 된다.
  ![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fa6c207e9-7541-4d57-914f-fa4452add7cd%2FUntitled.png?table=block&id=da9d6b32-668f-4529-aa49-f8ee22bb6345&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=530&userId=&cache=v2)
- let이나 const로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.
- 브라우저 환경의 모든 js 코드는 하나의 전역 객체를 공유한다. 여러 script 태그로 분리해도 같다.

### 빌트인 전역 프로퍼티, 메서드

- 빌트인 전역 프로퍼티
  - Infinity, NaN, undefined
- 빌트인 전역 함수
  - eval, isFinite, isNaN, parseFloat, parseInt, encodeURI/decodeURI, encodeURIComponent/decodeURIComponent
- 암묵적 전역
