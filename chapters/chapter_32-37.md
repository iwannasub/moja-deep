# 이선근 5주차 (32, 33, 35, 36, 37 장)

# 32. String

표준 빌트인 객체 String은 원시 타입인 문자열을 다룰 때 유용한 프로퍼티와 메서드를 제공한다.

- new 연산자와 함께 호출 ⇒ [[StringData]] 내부 슬롯에 인수로 전달받은 문자열을 할당한 String 래퍼 객체 생성
- [[PrimitiveValue]]는 [[StringData]] 내부 슬롯을 가리킨다.

```jsx
const str1 = new String('hi');
const str2 = String('hi');
const str3 = 'hi';
console.log(str1); // String {0: "h", 1: "i", length: 2, [[PrimitiveValue]]: "hi"}
console.log(str2); // hi
console.log(str3); // hi
```

String 래퍼 객체는 배열과 마찬가지로 length 프로퍼티와 인덱스를 나타내는 숫자형식의 문자열을 프로퍼티 키로, 각 문자를 프로퍼티 값으로 갖는 **유사 배열 객체**이면서 **이터러블**이다.

- 유사 배열 객체: 인덱스로 프로퍼티 값에 접근할 수 있고, length 프로퍼티를 갖는 객체

## 메서드

String 객체는 원본 String 래퍼 객체를 직접 변경하는 메서드는 존재하지 않는다.

⇒ 언제나 새로운 문자열을 반환한다.

문자열은 변경 불가능한 원시 값이기 때문에 String 래퍼 객체도 읽기 전용 객체로 제공된다.

```jsx
const str1 = new String('hi');
console.log(Object.getOwnPropertyDescriptors(str1));
// {
//   '0': {
//     value: 'h',
//     writable: false,
//     enumerable: true,
//     configurable: false
//   },
//   '1': {
//     value: 'i',
//     writable: false,
//     enumerable: true,
//     configurable: false
//   },
//   length: { value: 2, writable: false, enumerable: false, configurable: false }
// }
```

- search
  정규 표현식과 매치하는 문자열의 인덱스 반환, 실패시 -1
  `  const str4 = 'Hello world';
  console.log(str4.search(/o/)); // 4`
- substring
  slice와 동일하게 동작하지만 음수는 0으로 취급한다.
  `  console.log('hello'.substring(2, -1)); // he
  console.log('hello'.slice(2, -1)); // ll`

---

# 33. Symbol

ES6에서 도입된 7번째 데이터 타입. 변경 불가능한 원시 타입이다.

다른 값과 중복되지 않는 유일무이한 값이다. 주로 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용한다. (객체의 프로퍼티 키는 문자열과 심벌만 사용 가능)

## 생성

Symbol은 new 키워드로 생성 X

new로 생성자 함수를 호출하면 인스턴스(객체)가 생성되지만 심벌은 원시 값이다.

```jsx
const symbol = Symbol();
console.log(symbol); // Symbol()
```

인수로 문자열을 전달하면 심벌에 대한 설명을 추가할 수 있다. 디버깅 용도로 사용

심벌 값에는 영향을 주지 않는다.

심벌도 객체처럼 접근하면 래퍼 객체 생성

심벌은 암묵적으로 문자열, 숫자 타입으로 변환되지 않지만, 불리언 타입으로는 변환된다.

```jsx
const symbol = Symbol();

console.log(symbol + ''); // TypeError: Cannot convert a Symbol value to a string
console.log(+symbol); // TypeError: Cannot convert a Symbol value to a number
console.log(!symbol); // false
console.log(!!symbol); // true
```

### for, keyFor 메서드

```jsx
const s1 = Symbol('one');
const s2 = Symbol('one');
const s3 = Symbol.for('one');
const s4 = Symbol.for('one');

console.log(s1 === s2); // false
console.log(s1 === s3); // false
console.log(s3 === s4); // true

console.log(Symbol.keyFor(s1)); // undefined
console.log(Symbol.keyFor(s4)); // one
```

Symbol.for 메서드는 인수로 전달받은 문자열을 키로 사용해 키와 심벌 값의 쌍들이 저장되어있는 **전역 심벌 레지스트리**에서 해당 키와 일치하는 심벌 값을 검색한다.

검색 성공 시, 해당 값 반환하고 실패하면 새로 만들어 반환

Symbol을 사용하면 전역 심벌 레지스트리에서 검색할 수 있는 키를 지정할 수 없어서 등록, 관리 불가

keyFor를 사용하면 심벌 값의 키를 추출할 수 있다.

## 심벌과 프로퍼티 키

프로퍼티 키를 심벌로 만들면 다른 키와 절대 충돌하지 않는다.

심벌을 프로퍼티 키로 사용하려면 대괄호를 사용해야한다. 접근할 때도 대괄호 사용

```jsx
const obj = {
  [Symbol.for('mySymbol')]: 1,
};

console.log(obj[Symbol.for('mySymbol')]); // 1
```

## 프로퍼티 은닉

심벌 값을 프로퍼티 키로 사용하여 생성한 프로퍼티는 for in이나 Object.keys 같은 메서드로 찾을 수 없다.

```jsx
const obj = {
  [Symbol.for('mySymbol')]: 1,
  string: 2,
};

console.log(Object.getOwnPropertyNames(obj)); // [ 'string' ]
console.log(Object.getOwnPropertySymbols(obj)); // [ Symbol(mySymbol) ]
```

`getOwnPropertySymbols` 메서드를 사용해 값을 찾을 수 있다.

## 표준 빌트인 객체 확장

표준 빌트인 객체에 사용자 정의 메서드를 확장하는 것은 권장하지 않는다. 추후에 표준 사양으로 추가될 메서드 이름이 중복 될 수 있기 때문이다.

하지만 심벌을 사용하면 중복될 일이 없어 안전하게 확장 가능하다.

```jsx
Array.prototype.sum = function () {
  return this.reduce((acc, cur) => acc + cur);
};

console.log([1, 2].sum()); // 3

// 심벌로 프로퍼티 키를 생성하면 다른 키와 중복되지 않는다.
Array.prototype[Symbol.for('sum')] = function () {
  return this.reduce((acc, cur) => acc + cur);
};

console.log([1, 2][Symbol.for('sum')]()); // 3
```

## Well-known Symbol

자바스크립트가 기본 제공하는 심벌 값을 ECMAScript 사양에서 Well-known Symbol이라고 부른다.

자바스크립트 엔진의 내부 알고리즘에 사용된다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F4263306d-c0ef-4d64-8f8c-6d79ff2c0e75%2FUntitled.png?table=block&id=b81e5744-06a2-4e45-a21c-585111fba536&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1170&userId=&cache=v2)

---

# 35. 스프레드 문법

스프레드 문법은 여러 값들의 집합을 펼쳐서 개별적인 값들의 목록으로 만든다.

for…of 문으로 순회할 수 있는 이터러블에만 사용할 수 있다.

스프레드 문법의 결과는 값이 아니라 목록이다. 따라서 변수에 할당할 수 없다.

```jsx
const list = ...[1, 2, 3]; // SyntaxError: Unexpected token '...'
```

## rest와 spread

```jsx
function foo(...rest) {
  // [1, 2, 3]
  console.log(rest);
}

foo(...[1, 2, 3]); // 1, 2, 3
```

rest 파라미터는 인수들의 목록을 배열로 전달 받기 위해 사용. 즉 스프레드 문법과 반대의개념이다.

---

# 36. 디스트럭처링 할당

디스트럭처링 할당 (구조 분해 할당)은 이터러블 또는 객체를 비구조화하여 1개 이상 변수에 개별적으로 할당하는 것을 말한다.

필요한 값만 추출하여 할당 할 때 유용하다.

## 배열

배열 디스트럭처링 할당의 대상은 이터러블이어야하며, 할당 기준은 배열의 **인덱스**다.

그래서 개수가 일치할 필요 없이 순서대로 할당된다.

```jsx
const [a, b] = [1, 2, 3];
console.log(a, b); // 1 2
```

## 객체

할당 기준은 **프로퍼티 키**다. 그래서 순서는 의미 없고 변수 이름과 프로퍼티 키가 일치하면 할당된다.객체의 프로퍼티 키와 다른 변수 이름으로 값을 할당받으려면 :를 사용

```jsx
const { firstName: fn, lastName: ln } = {
  lastName: 'Lee',
  firstName: 'Seongeun',
};
console.log(fn, ln); // Seongeun Lee
```

---

# 37. Set과 Map

## Set

Set 객체는 중복되지 않는 유일한 값들의 집합이다.

배열과의 차이점

- 동일한 값 중복 X
- 요소 순서에 의미 X
- 인덱스로 요소 접근 X

### 생성

`new Set(이터러블 객체)`

이 때, 이터러블의 중복된 값은 저장되지 않는다.

```jsx
const set = new Set([1, 2, 3, 3]);
console.log(set); // Set(3) { 1, 2, 3 }
```

### 프로퍼티, 메서드

- size: 요소의 개수 확인
  getter 함수만 있는 접근자 프로퍼티로 변경 불가.
- add(): 요소 추가 후 추가된 Set 객체 반환

  ```
  const set = new Set([1, 2, 3, 3]);
  console.log(set); // Set(3) { 1, 2, 3 }

  set.add(4).add(5);
  console.log(set); // Set(5) { 1, 2, 3, 4, 5 }
  ```

- has(): 특정 요소가 존재하는지 확인하여 불리언 값 반환
- delete(): 특정 요소 삭제 후 성공 여부를 불리언 값으로 반환
- clear(): 모든 요소 삭제. undefined 반환
- forEach(): 요소 순회. 두 번째 인수도 현재 요소의 값이다. (배열에서는 인덱스)

## Map

키와 값의 쌍으로 이루어진 컬렉션이다.

객체와의 차이점

- 모든 값을 키로 사용할 수 있음
- 이터러블
- .size로 요소의 개수 확인

## 생성

`new Map(이터러블)`

이 때, 인수의 이터러블은 키와 값의 쌍으로 이루어져 있어야 한다.

중복은 덮어씌움

```jsx
const map1 = new Map([
  ['key', 'value1'],
  ['key', 'value2'],
]);
console.log(map1); // Map(1) { 'key' => 'value2' }

const map2 = new Map([1, 2]); // TypeError: Iterator value 1 is not an entry object
```

## 프로퍼티, 메서드

- size
- set(키, 값): 요소 추가 후 Map 객체 반환
- get(키): 전달한 키를 갖는 값을 반환. 없으면 undefined
- has()
- delete()
- clear()
- forEach(): 요소 순회. 두 번째 인수는 현재 요소의
