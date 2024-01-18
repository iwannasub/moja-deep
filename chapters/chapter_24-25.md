# 이선근 4주차 (24, 25 장)

# 24. 클로저

클로저는 자바스크립트 고유 개념이 아닌 함수로 일급 객체로 취급하는 함수형 프로그래밍 언어의 특성이다.

자바스크립트는 렉시컬 스코프를 따르는 프로그래밍 언어: 함수를 호출이 아닌 정의했을 때, 상위 스코프를 결정한다.

스코프의 실체 = 실행 컨텍스트의 렉시컬 환경
렉시컬 환경은 외부 렉시컬 환경에 대한 참조를 통해 상위 렉시컬 환경과 연결된다.

함수의 상위 스코프를 결정한다 => 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조 값을 결정한다.

함수가 호출되는 환경과 관계없이 정의된 환경(상위 스코프)를 기억해야한다. 이를 위해 [[Environment]] 내부 슬롯에 상위 스코프의 참조를 저장한다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fc8199f4d-5806-4f5e-8ff9-9407b66c7fd8%2FUntitled.png?table=block&id=f10d9c33-91a0-43fa-bf77-9d47648c4de0&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1640&userId=&cache=v2)

## 클로저와 렉시컬 환경

외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이런 중첩 함수를 클로저라고 한다.

```jsx
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };
  return inner;
}

const innerFunc = outer();
innerFunc(); // 10
```

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F118f097e-9531-4bd1-800c-c8cc437db8fa%2FUntitled.png?table=block&id=9d75528c-f6f0-4b80-8518-a481aefc089f&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1680&userId=&cache=v2)

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fdebd7f8e-b3ad-4b34-aab6-6b8587a02d9f%2FUntitled.png?table=block&id=8841b3b2-6ac9-4735-89fa-474774f1130d&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1680&userId=&cache=v2)

- 실행 컨텍스트 스택에서 제거되지만 렉시컬 환경까지 소멸하는 것은 아니다.
- 중첩함수가 외부 함수보다 오래 유지되어도 상위 스코프의 식별자를 참조하지 않는다면 상위 스코프를 기억하지 않는다.
- 상위 스코프의 변수 중 참조하고 있는 식별자만 기억하고 참조하지 않는다면 기억하지 않는다. 메모리 낭비 X

## 클로저의 활용

상태를 안전하게 변경하고 유지하기 위해 사용

의도치 않은 변경을 하지 않도록 은닉하고 특정함수에게만 상태 변경을 허용해 상태를 안전하게 변경, 유지하기 위해 사용한다.

**보조함수를 사용하는 클로저**

```jsx
function makeCounter(aux) {
  let counter = 0;

  return function () {
    counter = aux(counter);
    return counter;
  };
}

function increase(n) {
  return ++n;
}

function decrease(n) {
  return --n;
}

const increaser = makeCounter(increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

const decreaser = makeCounter(decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

- 상위 함수를 호출할 때마다 새로운 함수의 실행 컨텍스트가 생성되기 때문에 상태가 연동되지 않는다.
- 따라서 상위 함수는 한 번만 호출해야한다.

```jsx
const counter = (function () {
  let counter = 0;

  return function (aux) {
    counter = aux(counter);
    return counter;
  };
})();

function increase(n) {
  return ++n;
}

function decrease(n) {
  return --n;
}

console.log(counter(increase)); // 1
console.log(counter(increase)); // 2
console.log(counter(decrease)); // 1
console.log(counter(decrease)); // 0
```

---

# 25. 클래스

ES6에서 도입된 클래스는 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 문법적 설탕이라고 볼 수도 있다.

하지만 동일하게 동직하지 않고, 생성자 함수보다 엄하며 생성자 함수에서는 제공하지 않는 기능도 있다.

|                      | 생성자 함수                                         | 클래스                                                                    |
| -------------------- | --------------------------------------------------- | ------------------------------------------------------------------------- |
| new 연산자 없이 호출 | 일반 함수로 호출                                    | 에러 발생                                                                 |
| 상속 지원 키워드     | 없음                                                | extends, super                                                            |
| 호이스팅             | 선언문은 함수 호이스팅, 표현식은 변수 호이스팅 발생 | 호이스팅이 발생하지 않는 것처럼 동작                                      |
| strict mode          | 지정되지 않음                                       | 암묵적으로 strict mode가 지정되며 해제할 수 없음                          |
| [[Enumerable]]       | 열거 가능                                           | constructor, 프로토타입 메서드, 정적 메서드 모두 false로 열거되지 않는다. |

## 클래스 정의

- 선언문
- 표현식 (익명, 기명)

클래스는 함수이다. 따라서 값처럼 사용할 수 있는 일급 객체다.

클래스 몸체에서 정의할 수 있는 메서드: constructor, 프로토타입 메서드, 정적 메서드

```
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    console.log('Hi, my name is ' + this.name);
  }

  static sayHello() {
    console.log('Hello');
  }
}
```

```jsx
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype.sayHi = function () {
    console.log('Hi, my name is ' + this.name);
  };

  Person.sayHello = function () {
    console.log('Hello');
  };

  return Person;
})();
```

## 클래스 호이스팅

클래스 선언문 런타임 이전에 먼저 평가돼 함수 객체를 생성하지만, 정의 이전에 참조할 수 없다.

그래서 호이스팅이 발생하지 않는 것 처럼 보이나 그렇지 않다. let, const 처럼 선언한 변수처럼 호이스팅된다.

## 메서드

정의할 수 있는 메서드 3가지

- **constructor**
  인스턴스를 생성, 초기화하기 위한 특수한 메서드.
  클래스 정의가 평가되면 constructor의 기술된 동작을 하는 함수 객체가 생성된다.
  프로토타입의 constructor와 직접적인 관련 X. 프로토타입의 constructor는 모든 프로토타입이 가지고있는 프로퍼티로 생성자 함수를 가리킨다.
  class의 constructor는 메서드로 해석되는 것이 아니라 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다.
  생략 시 빈 constructor가 암묵적으로 정의된다. ⇒ 빈 객체 생성
- **프로토타입 메서드**
  클래스 몸체에서 정의한 메서드는 기본적으로 프로토타입 메서드가 된다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F04cf7441-b14b-460b-af39-685a6c480f7f%2FUntitled.png?table=block&id=237b2471-1cb6-4fb0-8ec3-c401bc0b1cf0&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1150&userId=&cache=v2)

- **정적 메서드**
  생성자 함수와 마찬가지로 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 의미. static 키워드를 붙이면 정적 메서드가 된다.
  ![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F8fbeabf8-cdfb-4a4c-bc4c-a84165b6f1eb%2FUntitled.png?table=block&id=c6b309e5-37f8-4c2c-822d-1d50357cf0e9&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1210&userId=&cache=v2)

      정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만, 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.
      인스턴스의 this는 인스턴스 자신을 가리키지만 클래스의 this는 인스턴스가 아닌 클래스를 가리키기 때문이다.

## 클래스의 인스턴스 생성 과정

1. 인스턴스 생성과 this 바인딩
   빈 객체 생성하고 인스턴스의 프로토타입으로 클래스의 프로토타입 프로퍼티가 가리키는 객체가 설정됨
   this는 클래스가 생성한 인스턴스를 가리킨다.
2. 인스턴스 초기화
   this에 바인딩 되어있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 초기화한다.
3. 인스턴스 반환
   인스턴스가 바인딩된 this가 반환된다.

```jsx
class Person {
  constructor(name) {
    // 1. 암묵적으로 인스턴스 생성, this에 바인딩
    console.log(this); // Person {}

    // 2. this에 바인딩되어 있는 인스턴스 초기화
    this.name = name;

    // 3. this를 암묵적으로 반환
  }
}
```

## 프로퍼티

- **인스턴스 프로퍼티**: constructor 내부에서 this에 추가한 프로퍼티는 언제나 클래스가 생성한 인스턴스의 프로퍼티가 된다.
- **접근자 프로퍼티**: 자체적으로 값을 가지지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티. getter는 get, setter는 set 키워드를 사용해 정의한다.
- **클래스 필드**: 클래스 필드(필드, 멤버)는 클래스 기반 객체지향 언어에서 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어.
  클래스 필드를 정의하는 경우 this에 클래스 필드를 바인딩 해서는 안 된다. this는 클래스의 constructor와 메서드 내에서만 유효하다.
  인스턴스 프로퍼티를 생성할 때 외부 초기값으로 초기화 할 필요가 있다면 constructor에서 정의하는 기존 방식을 사용하고, 아니라면 두 가지 모두 사용할 수 있다.
- **private 필드**: private 필드는 클래스 내부에서만 참조할 수 있다. 접근자 프로퍼티를 통해 간접적으로 접근해야한다. 필드의 선두에 `#`을 붙여 정의, 선언한다.
  private 필드는 클래스 몸체에 정의해야한다. constructor에 직접 정의하면 에러가 발생한다.
- **static 필드**: static 키워드를 사용해 정의

```jsx
class Person {
  firstName = 'Lee';
  #age = 26;

  static fullName = 'Lee Seongeun';

  constructor(lastName) {
    this.lastName = lastName;
  }

  get age() {
    return this.#age;
  }

  set age(newAge) {
    this.#age = newAge;
  }
}

const person = new Person('Seongeun');

console.log(Person.fullName);
console.log(person.lastName);
console.log(person.age);
```

## 상속에 의한 클래스 확장

기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것. 상위 클래스의 속성을 그대로 사용하고 자신만의 고유한 속성을 추가하여 확장한다.

### extends 키워드

```jsx
class Derived extends Base {}
```

- 수퍼클래스: 서브클래스에게 상속된 클래스
- 서브클래스: 상속을 통해 확장된 클래스

수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인 뿐만 아니라 클래스 간의 프로토타입 체인도 생성하기 때문에 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

**동적 상속**: extends 뒤에는 동적으로 상속 대상을 결정하는 표현식이 올 수 있다.

표현식은 [[Consturct]] 내부 메서드를 갖는 함수 객체로 평가되어야함. (클래스, 생성자 함수)

```jsx
class Derived extends (condition ? Base1 : Base2) {}
```

### 서브클래스의 constructor

서브클래스에서 constructor를 생략하면 다음과 같은 constructor가 암묵적으로 정의된다.

```jsx
 constructor(...args) { super(...args); } // 수퍼클래스의 constructor 호출
```

super 키워드: 호출 시 수퍼클래스의 constructor 호출, 참조 시 수퍼클래스의 메서드 호출

서브클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브클래스의 constructor 생략 불가

super 호출 전에는 this 참조 불가

super는 반드시 서브클래스의 constructor에서만 호출된다. 아니면 에러 발생

### 상속 클래스의 인스턴스 생성 과정

1. 서브클래스의 super 호출
   자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 위임한다. → 서브클래스의 constructor에 반드시 super를 호출해야하는 이유
2. 수퍼클래스의 인스턴스 생성과 this 바인딩
   수퍼클래스가 생성한 빈 객체에 this 바인딩. new.target은 서브클래스를 가리켜서 인스턴스는 서브클래스가 생성한 것으로 처리된다.
3. 수퍼클래스의 인스턴스 초기화
   this에 바인딩 되어있는 인스턴스에 프로퍼티를 추가하고 초기화한다.
4. 서브클래스 constructor로 복귀와 this 바인딩
   super 호출이 종료되고, 서브클래스는 인스턴스를 생성하지 않고 super가 반환한 인스턴스를 this에 바인딩하여 사용한다.
5. 서브클래스의 인스턴스 초기화
   super호출 이후 서브클래스의 constructor에 있는 인스턴스 초기화
6. 인스턴스 반환
   완성된 인스턴스가 바인딩된 this를 반환
