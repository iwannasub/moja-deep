# 이선근 6주차 (40, 41, 42장)

# 40. 이벤트

브라우저는 처리해야 할 특정 사건이 발생하면 이를 감지해 이벤트를 발생시킨다.

이벤트 발생 시, 호출될 함수를 **이벤트 핸들러**라고 하고, 브라우저에게 이벤트 핸들러의 호출을 위임하는 것을 **이벤트 핸들러 등록**이라고 한다.

## 이벤트 핸들러 등록

### 어트리뷰트 방식

HTML 요소의 속성 중 이벤트에 대응하는 이벤트 핸들러 어트리뷰트가 있다.

onclick과 같이 on 접두사 + 이벤트 타입으로 이루어져 있음

어트리뷰트 값으로 함수 참조가 아닌 함수 호출문 등의 문을 할당해야한다.

이벤트 핸들러 어트리뷰트 값은 암묵적으로 생성될 이벤트 핸들러의 함수 몸체를 의미

```jsx
// onclick="sayHi('Lee')"

function onclick(event) {
  sayHi('Lee');
}
```

### 프로퍼티 방식

DOM 노드 객체는 이벤트 핸들러 프로퍼티를 가지고 있다.

프로퍼티 키는 어트리뷰트와 마찬가지로 on 접두사 + 이벤트 타입

```jsx
const $button = document.querySelector('button');

$button.onclick = function () {
  console.log('click');
};
```

하나의 이벤트에 하나의 핸들러만 바인딩 할 수 있다.

### addEventListener 메서드 방식

```jsx
$button.addEventListener('click', function [, useCapture])
```

- 마지막 인수인 캡처 사용 여부는 생략 시 false로 버블링 단계에서 이벤트를 캐치한다.
- 이벤트 리스너는 하나 이상의 이벤트 핸들러를 등록할 수 있다. 등록 순서대로 호출

removeEventListener를 사용해 이벤트 핸들러 제거할 수 있다.

이 때, 전달한 인수가 일치하지 않으면 제거되지 않는다.

## 이벤트 객체

이벤트 발생 시, 이벤트 관련 정보를 담은 객체가 동적으로 생성되어 이벤트 핸들러의 첫 번째 인수로 전달된다.

이벤트 객체를 전달받으려면 이벤트 핸들러 정의할 때 매개변수를 명시적으로 선언해야 한다.

### 이벤트 객체의 상속 구조

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F3b9c9304-3e4b-4463-b87b-a10552646da2%2FUntitled.png?table=block&id=68006008-bc7c-4dc2-ad64-3063b5f0189f&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1380&userId=&cache=v2)

## 이벤트 전파

DOM 트리 상에 존재하는 DOM 요소 노드에서 발생한 이벤트는 DOM 트리를 통해 전파된다.

이를 활용해 이벤트를 발생시킨 타깃은 물론 상위 DOM 요소에서도 이벤트를 캐치할 수 있다.

```html
<body>
  <ul id="ul">
    <li id="a">a</li>
    <li id="b">b</li>
    <li id="c">c</li>
  </ul>
</body>
<script>
  const $ul = document.getElementById('ul');

  $ul.addEventListener('click', (e) => {
    console.log(e.eventPhase);
    console.log(e.target);
    console.log(e.currentTarget);
  });
</script>
```

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Ff0415025-7ae6-47e3-9338-13a4bdea05a2%2FUntitled.png?table=block&id=14374cb9-6fd7-4099-889e-0a443c44deb2&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=950&userId=&cache=v2)

1. 캡처링 단계: 이벤트가 상위 요소에서 하위 요소 방향으로 전파
2. 타깃 단계: 이벤트가 이벤트 타깃에 도달
3. 버블링 단계: 이벤트가 하위 요소에서 상위 요소 방향으로 전파

버블링되지 않는 이벤트

- 포커스 이벤트: focus, blur
- 리소스 이벤트: load, unload, abort, error
- 마우스 이벤트: mouseenter, mouseleave

위 요소들은 상위 요소에서 캐치하려면 캡처링 단계의 이벤트를 캐치해야한다.

## 이벤트 위임

여러 개의 하위 DOM 요소에 각각 이벤트 핸들러를 등록하는 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법

```html
<body>
  <ul id="ul">
    <li id="a">a</li>
    <li id="b">b</li>
    <li id="c">c</li>
  </ul>
  <div>
    선택된 아이템
    <span class="msg"></span>
  </div>
</body>
<script>
  const $ul = document.getElementById('ul');
  const $msg = document.querySelector('.msg');

  function activate({ target }) {
    [...$ul.children].forEach(($li) => {
      $li.classList.toggle('active', $li === target);
      $msg.textContent = target.id;
    });
  }

  document.getElementById('a').onclick = activate;
  document.getElementById('b').onclick = activate;
  document.getElementById('c').onclick = activate;
</script>

// 위임

<body>
  <ul id="ul">
    <li id="a">a</li>
    <li id="b">b</li>
    <li id="c">c</li>
  </ul>
  <div>
    선택된 아이템
    <span class="msg"></span>
  </div>
</body>
<script>
  const $ul = document.getElementById('ul');
  const $msg = document.querySelector('.msg');

  function activate({ target }) {
    if (!target.matches('#ul > li')) return;

    [...$ul.children].forEach(($li) => {
      $li.classList.toggle('active', $li === target);
      $msg.textContent = target.id;
    });
  }

  $ul.onclick = activate;
</script>
```

## DOM 요소의 기본 동작

a 요소 ⇒ href 링크로 이동, checkbox ⇒ 체크 or 해제 등 DOM 요소는 기본 동작이 있다.

**preventDefault** 메서드로 DOM 요소의 기본 동작을 중단한다.

stopPropagation 메서드는 이벤트 전파를 중지시킨다.

하위 요소에서 stopPropagation을 하면 상위 요소에서 캐치할 수 없다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <ul id="ul">
      <li id="a">a</li>
      <li id="b">b</li>
      <li id="c">c</li>
    </ul>
  </body>
  <script>
    const $ul = document.getElementById('ul');

    $ul.addEventListener('click', (e) => {
      console.log(e.eventPhase);
      console.log(e.target);
      console.log(e.currentTarget);
    });

    document.getElementById('b').onclick = (e) => {
      e.stopPropagation();
    };
  </script>
</html>
```

## 이벤트 핸들러 내부의 this

### 어트리뷰트 방식

일반 함수와 같이 전역 객체 window를 가리키지만,

이벤트 핸들러를 호출할 때 인수로 전달한 this는 이벤트를 바인딩한 DOM요소를 가리킨다.

```html
<body>
  <button onclick="handleClick(this)">click me</button>
</body>
<script>
  function handleClick(arg1) {
    console.log(arg1);
  }
</script>
```

### 프로퍼티 방식, addEventListener 방식

프로퍼티와 addEventListener 방식에서 이벤트 핸들러 내부의 this는 이벤트를 바인딩한 DOM 요소를 가리킨다. == currentTarget

화살표 함수로 정의한 핸들러 내부의 this는 상위 스코프의 this를 가리킨다.

화살표 함수는 자체 this 바인딩을 갖지 않기 때문

클래스에서는 this 사용시 인스턴스를 가리키도록 바인딩 해줘야한다. 또는 화살표 함수 사용

화살표 함수를 사용하면 프로토타입 메서드가 아닌 인스턴스 메서드가 된다.

## 이벤트 핸들러에 인수 전달

1. 이벤트 내부에서 함수를 호출하면서 전달

   ```
   <body>
       <button id="input">click me</button>
     </body>
     <script>
       const $input = document.getElementById('input');
       const myName = 'lee';

       function handleClick(arg1) {
         console.log(arg1);
       }

       $input.onclick = () => {
         handleClick(myName);
       };
     </script>
   ```

2. 이벤트 핸들러를 반환하는 함수를 호출하면서 인수 전달

   ```html
   <body>
     <button id="input">click me</button>
   </body>
   <script>
     const $input = document.getElementById('input');
     const myName = 'lee';

     function handleClick(arg1) {
       return function (e) {
         console.log(arg1);
       };
     }

     $input.onclick = handleClick(myName);
   </script>
   ```

---

# 41. 타이머

## 호출 스케줄링

호출 스케줄링: 함수를 명시적으로 호출하지 않고 일정 시간이 경과된 이후 호출되도록 함수 호출을 예약하는 것

타이머 함수는 표준 빌트인 객체가 아닌 브라우저 환경, Node.js 환경에서 제공하는 호스트 객체다.

타이머 함수 setTimeout, setInterval이 생성한 타이머가 만료되면 콜백 함수가 호출된다.

타이머 함수는 비동기로 동작한다.

## 타이머 함수

### setTimeout, clearTimeout

```jsx
const timeoutID = setTimeout(function[, delay, arg1, arg2, ...]);
```

- function: 타이머 만료 시 호출될 콜백 함수
- delay: 타이머 만료 시간. ms 단위. 기본값 0
- arg…: 콜백에 전달할 인수
- 타이머 id를 반환. clearTimeout에 타이머 id를 전달해 취소할 수 있다.

### setInterval, clearInterval

두 번째 인수로 전달받은 시간마다 반복 동작하는 타이머 생성

## 디바운스와 스로틀

scroll, input 같은 이벤트는 짧은 간격으로 연속해서 발생한다.

이런 이벤트 핸들러는 과도하게 호출되어 성능에 문제를 일으킬 수 있다.

**디바운스**

연속해서 이벤트 발생시 핸들러를 바로 호출하지 않고 일정 시간 경과 후 한 번만 호출되도록 한다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2Fc9f79fbf-6e14-4eee-b974-96adf742899d%2FUntitled.png?table=block&id=72cbce60-bd86-4f18-8794-c349b92dbaa6&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1410&userId=&cache=v2)

**스로틀**

연속해서 발생하는 이벤트를 그룹화해서 일정 시간 단위로 이벤트 핸들러가 호출되도록 호출 주기를 만든다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F3a57459a-b097-4a95-8976-a4103618fcbb%2FUntitled.png?table=block&id=748cac56-9df9-44e8-9752-fcb5925d0fd0&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=1480&userId=&cache=v2)

---

# 42. 비동기

함수가 실행되려면 함수 코드 평가 과정에서 생성된 함수 실행 컨텍스트가 실행 컨텍스트 스택에 푸쉬 되어야한다.

실행 컨텍스트 스택에 푸시 === 함수 실행의 시작

자바스크립트 엔진은 한 개의 실행 컨텍스트 스택을 갖는다.

⇒ 동시에 2개 이상의 함수를 동시에 실행할 수 없다는 것을 의미 === 싱글 스레드

싱글 스레드는 한 번에 하나만 실행할 수 있기 때문에 처리에 시간이 걸리는 태스크를 실행하는 경우 블로킹이 발생한다.

- 동기 처리
  - 현재 실행 중인 태스크가 종료할 때까지 다음 태스크가 대기하는 방식
  - 실행순서가 보장되지만 블로킹되는 단점이 있다.
- 비동기 처리
  - 현재 실행 중인 태스크가 종료되지 않은 상태에서 다음 태스크를 실행하는 방식
  - 블로킹되지 않지만 실행순서가 보장되지 않는다.

## 이벤트 루프와 태스크 큐

이벤트 루프: 브라우저에 내장되어 있는 기능 중 하나로 자바스크립트의 동시성을 지원한다.

![Untitled](https://snkn.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F9fb2262b-2219-432d-87b6-fbfb7bdda6ed%2F696e5c81-c5a3-42d0-a8bd-52ee0fd1ed25%2FUntitled.png?table=block&id=55c5e292-7040-446b-98f6-4ed235fb9461&spaceId=9fb2262b-2219-432d-87b6-fbfb7bdda6ed&width=950&userId=&cache=v2)

자바스크립트 엔진의 2개 영역

- 콜 스택
  = 실행 컨텍스트 스택. 함수 호출 시 콜 스택에 푸시되어 순차적으로 실행.
- 힙
  객체가 저장되는 메모리 공간

비동기처리에서 소스코드의 평가와 실행을 제외한 모든 처리는 자바스크립트 엔진 구동 환경인 브라우저 또는 Node.js가 담당한다.

이를 위해 브라우저 환경은 태스크 큐와 이벤트 루프를 제공한다.

- 태스크 큐
  비동기 함수의 콜백 함수나 이벤트 핸들러가 일시적으로 보관되는 영역
- 이벤트 루프
  콜 스택에 현재 실행 중인 컨텍스트, 태스크 큐에 대기중인 함수가 있는지 반복해서 확인한다.
  콜스택이 비어있고 태스크 큐에 대기 중인 함수가 있다면 태스크 큐에 대기중인 함수를 콜 스택으로 이동시킨다.

```jsx
function foo() {
  console.log('foo');
}

function bar() {
  console.log('bar');
}

setTimeout(foo, 0); // 0은 실제로 4ms
bar();
```
