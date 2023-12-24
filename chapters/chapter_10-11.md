# [모자딥 스터디] 10장 ~ 11장

## 10장 - 객체 리터럴

### 객체의 정의와 구성

자바스크립트에서는 원시 값을 제외한 나머지값(함수, 배열, 정규 표현식)은 모두 객체다.
여기서 `객체` 는 다양한 타입의 값이 하나의 단위로 구성되어 있는 `복합적인 자료구조(data structure)`이다.

객체는 `프로퍼티(property)`의 집합이고, 프로퍼티는 `키(key)`+`값(value)`로 구성되어 있다.

```ts
const person = {
  name: "Yoon", // <- 프로퍼티 = 키('name') + 값('Yoon')
  age: "Seungrok", //<- 프로퍼티 = 키('age') + 값('Seungrok')
};
```

### 객체 생성 방법 - 리터럴을 통한 객체 생성

자바스크립트에서는 객체를 생성하는 방법이 여러가지이다.

- 객체 리터럴
- Object 생성자 함수
- 생성자 함수
- Object.create 메서드
- 클래스(ES6)

```ts
//객체 생성 방법
const person = {
  name: "Yoon", // <- 프로퍼티 = 키('name') + 값('Yoon')
  age: "Seungrok", //<- 프로퍼티 = 키('age') + 값('Seungrok')
};
console.log(person);

const table = new Object(1);
table.foo = 42;
console.log(table);

const num = Number(1);
console.log(num);

function Person(name, age) {
  this.name = name;
  this.age = age;
  this.sayHello = function () {
    console.log(
      `Hello, my name is ${this.name} and I am ${this.age} years old.`
    );
  };
}
const seugnrok = new Person("Seungrok", 31);

const obj = Object.create({ name: "yoon" });
console.log(obj.__proto__);
const obj2 = Object.create(null);
console.log(obj2.__proto__);
const obj3 = Object.create(Number);
console.log(obj3.__proto__);

class SomeClass {
  constructor() {
    this.data = [];
  }
}
const someClassInstance = new SomeClass();
console.log(someClassInstance);
```

생성자 함수
https://www.linkedin.com/pulse/class-object-constructor-function-javascript-amendra-botejue/

### 프로퍼티

`프로퍼티 키`는 객체의 프로퍼티 값에 접근할 수 있는 이름이며, 식별자 역할을 한다.

식별자이기에, 자바스크립트의 식별자 네이밍 규칙을 따라야 할 것 같지만, 그렇지 않다.다만, 식별자 네이밍 규칙을 따르지 않는 이름을 프로퍼티 키로 사용할 때는 반드시 `따옴표`를 사용해야 한다.

```ts
const aObject = {
  _keyNameWithValidIdentifierConvention: 1,
  "1_keyNameWithInValidIdentifierConvention": 1,
};
console.log(aObject._keyNameWithValidIdentifierConvention);
console.log(aObject["1_keyNameWithInValidIdentifierConvention"]);
```

### 메서드

자바스크립트에서 `함수는 1급 객체`이다. 이 말은, `함수는 값으로 사용될 수 있다`는 의미이며, 어딘가에 할당하여 사용할 수 있다는 말이다.

객체의 프로퍼티 값으로 함수를 할당할 수 있는데, 이렇게 프로퍼티 값이 함수일 경우에는 일반 함수와 구분하기 위해 `메서드`라 부른다. 메서드는 즉 `객체에 묶여 있는 함수`이다.

```ts
const circle = {
  radius: 5,
  getDiameter: function () {
    return 2 * this.radius;
  },
};
console.log(circle.getDiameter());
```

### 실습

> Object 생성자함수를 사용하여, key가 "\*+1234_foo" , 값이 1인 프로퍼티를 추가해보시오. 그리고 생성한 객체에서 '마침표 표기법'과 '대괄호 표기법'으로 각각 프로퍼티에 접근을 시도하고 결과에 대해 설명하시오.

> 아래 객체에서 name값을 'Yoon'으로 변경하고, 불변성에대해 설명하시오.

```ts
const person = {
  name: "Lee",
  first: "Rok",
};
```

> ['seungrok','seungwon','sunkeun'] 이름 데이터를 가지고, 아래 객체에 '{인덱스}\_{이름}' : {이름} 형식의 프로퍼티를 동적으로 추가해보라. \*유동적인 데이터길이에 유연하게 대처할 수 있는 코드를 작성해보도록 노력해보자.

```ts
const people = {};
```

## 11장 - 원시 값과 객체의 비교

### 원시값은 불변하다!

원시 값 - 메모리 공간에 `실제 값`이 저장(pass by value)
객체 - 변수가 가리키는 메모리 공간에는 `참조 값`이 저장(pass by reference)

> 원시 값은 불변하다 = 원시 값 자체를 변경할 수 없다는 말
> != 변수 값을 변경할 수 없다는 말

원시 값은 값을 재할당 하면, 변수는 새롭게 재할당한 원시 값을(저장하고 있는 새로운 메모리주소)를 가리킨다. 이것은 변수가 참조하고 있는 메모리 공간의 주소가 바뀐는 의미이다. 이러한 원시값의 특성을 `불변성(immutability)`라 한다.

### 원시 타입인 문자열

```ts
var str = "Hi";
str = "Hello";
```

자바스크립트에서는 문자열도 원시타입, 즉 불변하는 타입이다.

위 코드에서는 str변수에 "Hi"를 할당했다가 "Hello"로 재할당을 하고 있다. 이 때 벌어지는 일은 다음과 같다.

- "Hi" 문자열을 저장할 메모리 공간을 확보하고, 그 첫번째 주소를 str이 가리킨다.
- "Hello" 문자열을 메모리에 생성하고, 식별자 str이 이것을 가리킨다.

"Hi"가 저장되어있는 메모리 공간에 "Hello"를 덮어씌우는 것이 `절대 아님`을 기억하자.

```ts
var word = "abcde";
word[0] = "f";
console.log(word);
```

### 원시타입 - pass by value

```ts
let score = 80;
let copy = score;
score = 100;
console.log(copy);
```

80이라는 값은 원시타입인 Number이다. 원시타입은 다른 변수에 할당될 때, 값이 복사되어(복사된 값이 저장되어있는 메모리주소가 변수로) 전달된다.

![](https://velog.velcdn.com/images/seungrok-yoon/post/b605f66e-598b-4eb8-b8a9-b1172eb68e28/image.png)

`변수가 가리키고 있는 메모리 주소에는 그 값이 존재` 하는것이다!

### 객체타입 - pass by reference

`변수가 가리키고 있는 메모리 주소에는 그 값이 존재` 하는 원시타입과는 달리,
객체타입에서는 변수가 가리키고 있는 메모리 주소에 - `객체가 저장되어있는 주소값`이 존재한다!

![](https://velog.velcdn.com/images/seungrok-yoon/post/130d1d3b-d8d4-41b2-abd6-ad8de1661a69/image.png)

```ts
const scores = { math: 80, korean: 70 };
const copy = scores;
scores.math = 100;
console.log(copy);
```

> 객체를 복사해 생성하는 비용을 절약하여 성능을 향상시키기 위해 객체는 변경 가능한 값으로 설계되어 있다. 그렇지만, 이런 구조의 단점으로 여러 개의 식별자가 하나의 객체를 공유할 수 있는 치명적인 부작용이 있다.

```ts
const names = { name: "hi" };
const newNames = { name: "hi" };
const copiedNames = names;
console.log(names === newNames);
console.log(names === copiedNames);

names.ages = 50;
console.log(newNames);
console.log(copiedNames);
```

![](https://velog.velcdn.com/images/seungrok-yoon/post/e4698e1c-6ddb-4b0c-9aa8-6184d47b7b02/image.png)

> "값에 의한 전달", "참조에 의한 전달" 모두 식별자가 기억하는 메모리 공간에 저장되어 있는 `어떠한 값`을 전달한다는 면에서는 동일하다.
> 다만, 그 값이 원시값인지, 참조 값인지에 차이가 있을 뿐이다.

따라서, 자바스크립트에서는 "값에 의한 전달"만이 존재한다고 말할 수 있다!

## 추가적인 의문 - 객체 생성 방식들의 차이

### Object.create로 살펴보는 프로토타입

> Object

## React에서의 불변성

> 아래 코드는 React의 props를 전달하는 예시입니다. 아래 코드의 문제점이 무엇인지, 객체의 불변성을 기준으로 설명해보시오.
