# 이선근 7주차 (46, 47, 48장)

# 46. 제너레이터 async/await

코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다.

제너레이터와 일반 함수의 차이

1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.
2. 함수 호출자와 함수의 상태를 주고받을 수 있다.
3. 호출하면 제너레이터 객체를 반환한다.

## 제너레이터 함수 정의

```jsx
function* genFunc() {
  yield 1;
}
```

- `function*` 키워드로 선언
- 하나 이상의 `yield` 표현식을 포함

그 외에는 일반 함수와 같지만 화살표 함수로 정의 불가, new와 함께 생성자 함수로 호출 불가

\*의 위치는 function 키워드와 함수 이름 사이 어디든지 상관없지만 function 키워드 바로 뒤 권장

## 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 코드 블럭을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환한다.

제너레이터 객체는 **이터러블**이면서 **이터레이터**다.

제너레이터 객체는 이터레이터에 없는 return, throw 메서드를 갖는다.

- return: 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
- throw: 인수로 전달받은 에러 발생, undefined를 value, true를 done으로 갖는 리절트 객체 반환.

```jsx
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.log(e); // Error
  }
}

const generator = genFunc();

console.log(generator.next()); // { value: 1, done: false }

console.log(generator.return('End')); // { value: "End", done: true }
console.log(generator.throw('Error')); // { value: undefined, done: true }
```

## 일시 중지와 재개

`yield` 키워드와 `next` 메서드를 통해 실행을 일시 중지 했다가 필요한 시점에 다시 재개할 수 있다.

<aside>
💡 next 메서드 호출 ⇒ yield 표현식까지 실행 후 일시 중지 ⇒ next() ⇒ yield ⇒ … ⇒ return

</aside>

- 이터레이터의 next와 달리 제너레이터의 next 메서드에는 인수 전달 가능
- 제너레이터 객체의 next에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당된다.

```jsx
function* getFunc() {
  const x = yield 1;
  const y = yield x + 10;
  return x + y;
}

const generator = getFunc();

let res = generator.next();
console.log(res);

res = generator.next(10);
console.log(res);

res = generator.next(20);
console.log(res);
```

## 제너레이터의 활용

### 이터러블 구현

```jsx
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num);
}
```

### 비동기 처리

```jsx
const async = (generatorFunc) => {
  const generator = generatorFunc();

  const onResolved = (arg) => {
    const result = generator.next(arg);

    return result.done
      ? result.value
      : result.value.then((res) => onResolved(res));
  };

  return onResolved;
};

async(function* fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = yield fetch(url);
  const todo = yield response.json();
  console.log(todo);
})();
```

---

# 47. 에러 처리

에러가 발생했을 때 방치하면 프로그램은 강제 종료된다.

에러 처리를 통해 프로그램이 강제 종료 되는 것을 막고 계속해서 코드를 실행시킬 수 있다.

**try…catch…finally 문**

- try: 에러가 발생할 가능성이 있는 코드
- catch: try 코드 블록에서 에러가 발생하면 이 코드 실행
- finally: 에러 발생 여부 상관없이 실행

```jsx
try {
  foo();
} catch (error) {
  console.error(error);
} finally {
  console.log('finally');
}
```

## Error 객체

생성자 함수에 에러 메시지 전달해서 에러 객체 생성 가능

```jsx
const error = new Error('error');
const typeError = new TypeError('typeError');
```

**프로퍼티**

- message: 생성자 함수에 인수로 전달한 에러 메시지
- stack: 에러를 발생시킨 콜스택의 호출 정보를 나타내는 문자열로, 디버깅 용도로 사용

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F10f97b15-31a9-4867-b973-23cd844a7ba1%2FUntitled.png?table=block&id=ba2e8592-0ac0-4405-b28e-96fe3cd95ca4&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=910&userId=&cache=v2)

## throw 문

```jsx
throw new Error('Error');
```

에러 객체 생성 ≠ 에러 발생

throw 문으로 던져야 에러가 발생한다.

throw는 표현식의 어떤 값이라도 상관없지만 일반적으로 에러 객체를 지정한다

에러를 던지면 catch문의 에러 변수가 생성되고 던져진 에러 객체가 할당된다. 그리고 catch 블럭이 실행된다.

```jsx
try {
  throw 'hello';
} catch (error) {
  console.log(error); // hello
}
```

## 에러의 전파

에러는 호출자 방향으로 전파된다.

콜 스택의 아래 방향으로 전파된다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F4944ff5c-78ae-4167-9fb7-d1bf3f5bd181%2FUntitled.png?table=block&id=dd093547-5ed6-4113-977c-d6b9e16d900f&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=670&userId=&cache=v2)

setTimeout이나 프로미스 후속 처리 메서드의 콜백 함수는 콜 스택이 비면 이벤트 루프에 의해 콜 스택으로 푸시된다. 이 때 콜백 함수의 실행 컨텍스트는 콜 스택 제일 아래에 있기 때문에 호출자가 없다.

```jsx
try {
  setTimeout(() => {
    throw new Error('error in callback');
  }, 0);
} catch (error) {
  console.error(error.message);
}
```

---

# 48. 모듈

모듈: 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각.

모듈이 성립하려면 자신만의 파일 스코프(모듈 스코프)를 가질 수 있어야 한다.

자바스크립트는 모듈 시스템을 지원하지 않기 때문에 여러 파일로 분리해 script 태그로 로드해도 하나의 파일 내에 있는 것처럼 동작한다.

CommonJS와 AMD의 등장으로 Node.js 환경에서 모듈 시스템 지원

## ES6 모듈(ESM)

ES6에서는 클라이언트 사이드 JS에서도 동작하는 모듈 기능을 추가했다.

```jsx
<script type='module' src='app.mjs'></script>
```

script 태그에 type=”module” 어트리뷰트 추가하면 js파일은 모듈로 동작한다.

이 때 일반적인 js파일이 아니라 ESM임을 명시하기 위해 .mjs 확장자 사용을 권장한다.

ESM은 독자적인 모듈 스코프를 갖는다.

따라서 모듈 내에서 var 키워드로 선언한 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.

### export

모듈의 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있게 하려면 export 키워드를 사용한다.

- export 키워드를 선언문 앞에 사용
  `export function() {...}`
- 하나의 객체로 구성에 한번에 export
  `function a () {}
function b () {}
export { a, b }`

### import

다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드하려면 import 키워드를 사용한다.

- - as 를 사용해 객체의 프로퍼티로 모아 import
    `import * as 식별자 from 모듈 파일`
- 하나의 값만 export 한다면 default 키워드 사용할 수 있음
  default는 var, let, const 키워드 사용 불가
- default로 export한 모듈은 {} 없이 임의의 이름으로 import
