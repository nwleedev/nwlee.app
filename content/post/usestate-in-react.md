---
title: 'React의 useState 작동원리'
description: 'React Hooks의 useState가 컴포넌트 내부에서 어떻게 작동하는지 파악해봤습니다.'
date: 2022-03-15T12:42:05+09:00
image: 'https://static.nwlee.com/public/L0RDBEO0/8bae5cf2-a727-4ea2-bb7d-2e1560e9c56f.png'
math:
license:
hidden: false
comments: true
draft: true
tags: ['React', 'React Hooks', '리액트', 'useState', '컴포넌트']
categories: ['React']
---

# useState

리액트 함수 컴포넌트에서 상태 변수를 선언할 때 useState 함수를 호출할 수 있다. useState에 초기값을 매개변수로 입력해주면 현재 상태 값과 상태를 업데이트할 수 있는 함수를 반환받을 수 있다. 매개변수의 초기값은 컴포넌트가 렌더링될 때 딱 한 번만 사용된다.

```JSX
const [counter, setCounter] = useState(0)
```

## 왜 useState를 사용해야 하는가?

면접에서 이러한 질문을 받은 적이 있었다.

```
함수 컴포넌트에서 상태 변수를 선언하는데 왜 useState를 사용해야 되는가? let으로 선언하면 값 변경도 자유롭게 할 수 있지 않나?
```

```TSX
const App = () => {
  let counter = 0;
  setInterval(() => {
    counter++;
  }, 1000);
  return (
    <div>
      <h1>Hello React! {counter} Times</h1>
    </div>
  );
};
```

let으로 counter 변수를 선언한 다음, setInterval로 1초마다 counter가 1 증가할 수 있게 했다. 실제로 실행해보면 counter가 0으로 고정되는 것을 볼 수 있다. 렌더링되는 counter의 값이 변경되면 리액트는 컴포넌트를 다시 실행시키므로 let으로 선언된 counter는 다시 0으로 초기화된다.

## 가상 돔(Virtual DOM)

웹 브라우저는 HTML 문서를 받아서 DOM 노드로 이루어진 트리를 생성한다. 그리고 CSS 파일을 읽어 각 노드들의 스타일 속성들을 적용시킨다. 렌더링할 노드 트리가 전부 생성되면 웹 페이지가 렌더링된다.

만약 DOM에 변화가 생기면 이 과정을 전부 다시 실행하게 된다. DOM 변경이 자주 발생하게 되면 브라우저는 레이아웃 계산 후 렌더링하는 과정을 반복해야 한다.

가상 돔은 UI의 표현을 실제 DOM이 아닌 메모리에 저장하고 ReactDOM로 실제 DOM과 동기화하는 프로그래밍 개념이다. 리액트에서는 모든 DOM마다 해당하는 가상 돔 객체가 생성된다. 가상 돔에 변경이 발생하면 화면에 바로 적용되지 않고 두 가상 돔끼리 비교하는 과정을 거쳐 어떤 DOM 객체가 업데이트되어야 할지 파악한다.. 리액트는 전체 DOM을 다시 렌더링하는 대신 업데이트해야 할 DOM 객체만 변경시킨다.

위 예시에 있는 컴포넌트는 자바스크립트(타입스크립트) 함수이다. 그래서 컴포넌트에 렌더링되는 counter의 값이 변경될 때마다 컴포넌트를 업데이트하기 위해 함수가 다시 실행되는 것이다.

답변은 리액트 훅스 공식문서에서 쉽게 찾을 수 있었다. useState로 생성되는 상태 변수는 컴포넌트가 렌더링될 때 딱 한 번만 생성된다. 상태 변수의 값이 변경되어 컴포넌트를 다시 렌더링해야 할 때 useState는 새로운 상태 변수를 생성하는 것이 아닌 현재 상태 값을 반환해준다.
