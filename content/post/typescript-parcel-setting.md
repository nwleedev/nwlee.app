---
title: '타입스크립트 & 파셀 개발 환경 설정'
description: '타입스크립트와 파셀(Parcel)로 웹 프론트엔드 개발 환경을 빠르게 구축할 수 있습니다.'
date: 2022-03-09T00:06:19+09:00
image: 'https://static.nwlee.app/public/L0HD8G00/d3e1a180-c24a-4243-a721-96bc623ccf98.png'
math:
license:
hidden: false
comments: true
draft: true
tags: ['TypeScript', 'Parcel', 'Environment', '파셀', '개발 환경']
categories: ['TypeScript']
---

## 목적

자바스크립트 기초 지식을 공부하기 위해 [모던 자바스크립트 튜토리얼](https://ko.javascript.info) 웹 사이트를 자주 이용하고 있다. 학습 주제마다 예제와 과제가 주어지는데 자바스크립트 대신 타입스크립트로 실습하고 싶었다. 타입스크립트로 각 변수, 매개변수마다 타입을 지정해 줄 수 있기 때문이었다.

타입스크립트는 웹 브라우저가 인식하지 못해서 tsc와 같은 명령어를 사용해 자바스크립트로 변환해줘야 한다. 브라우저에서 최신 자바스크립트 문법이 잘 작동하게 하기 위해 Babel로 트랜스파일링하는 과정이 필요하다. 학습하면서 생성한 모듈의 수가 많아진다면 Webpack과 같은 모듈 번들러를 사용해 하나의 파일로 용량을 낮춰야 했다.

그동안 Webpack, Babel 등으로 타입스크립트 또는 리액트 & 타입스크립트 개발 환경을 직접 구성해보려고 했다. 코드를 작성하고 개발 서버에서 실행시키는 과정에서 오류가 종종 발생했다. 어디에서 오류가 발생했는지 그리고 오류의 원인이 무엇인지 시간을 낭비하는 점은 마음에 들지 않았다. 한 번 환경 구축하기 위해서 여러가지 설정하는 것도 귀찮았다.

Parcel을 발견했고, 한번 사용해보기로 했다.

## Parcel

공식문서에 따르면 별도의 설정이 필요없는(Zero Configuration) 웹 어플리케이션 번들러라고 적혀있다. 한국어 공식문서도 존재하고, 공식문서에 있는 설명만으로 타입스크립트 웹 프론트 개발환경을 쉽게 구축할 수 있었다.

영어 공식문서에서는 각 파일에 대한 플러그인들을 자동으로 설치해준다고 적혀있었다. 웹팩과 같이 개발 서버와 핫 리로딩 전부 지원한다. 핫 리로딩은 자바스크립트 코드에 변경점이 생길 경우 브라우저에 반영되는 것이라고 보면 될 것 같다.

개발 서버를 실행시켰을 때 사용자가 브라우저에서 특정 페이지를 요청할 때까지 그 페이지에 필요한 파일들을 빌드하는 과정을 지연시킨다고 적혀있다. 프로젝트에 많은 모듈이 작성되어 있어도 개발 서버가 작동하는 시간에는 큰 영향을 주지 않는다는 의미로 보인다. CommonJS 모듈과 ES 모듈 전부 지원한다.

![Lazy Dev Builds](https://static.nwlee.app/public/L0HD8G00/d5b1de14-356f-4875-bc4e-81e68fb1d2db.png)

참고로 Parcel에는 한국인 개발자가 개발한 트랜스파일러 [SWC](https://swc.rs/)가 사용된다. SWC는 러스트(Rust) 언어로 작성되어 바벨보다 수십 배나 빠르다.

## 개발 환경 설정

우선 yarn 또는 npm으로 Parcel을 전역적(Global)으로 설치한다. 여러 개의 프로젝트에 번들러를 사용할 수 있다. 그리고 노드 프로젝트를 하나 생성한다.

```PS
npm install -g parcel-bundler

yarn init -y
```

그 다음 번들러의 진입점이 될 수 있는 HTML 파일을 우선 하나 생성한다. 특이한 점은 HTML의 스크립트 태그에 타입스크립트 파일을 그대로 명시할 수 있다는 점이다.

타입스크립트 개발할 때 추가 설정을 해주기 위해 tsconfig.json 파일을 생성했다. Parcel은 타입 검사를 따로 수행하지 않기 때문에 tsc --noEmit 명령어를 실행시켜 타입스크립트가 파일을 검사하게 할 수 있다. 컴파일러 옵션에서 noEmit 속성을 true로 설정하면 따로 --noEmit 태그를 달아줄 필요가 없다.

```PS
yarn tsc --init
```

```JSON
{
  "compilerOptions": {
    "target": "ES5",
    "lib": [
      "ES2015",
      "ES2016",
      "ES2017",
      "ES2018",
      "ESNext",
      "DOM",
      "ScriptHost"
    ],
    "outDir": "./build",
    "noEmit": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noImplicitThis": true,
    "skipLibCheck": true
  }
}
```

public 폴더 내 index.html 파일이 있고, src 파일에 타입스크립트 파일이 있다.

```HTML
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <title>SPA with Parcel</title>
</head>
<body>
  <div id="root"></div>
  <script src="../src/index.ts"></script>
</body>
</html>
```

```TypeScript
// src/index.ts
(() => {
  const $app = document.getElementById('root');
  if (!$app || !($app instanceof HTMLDivElement)) {
    return;
  }
  const handleClick = (e: MouseEvent) => {
    if (e.target instanceof HTMLAnchorElement) {
      const userId = e.target.dataset['userId'];
      window.history.pushState({}, '', `users/${userId}`);

      User($app, (e) => {}, parseInt(userId ? userId : '0'));
    }
  };

  window.addEventListener('popstate', () => {
    const path = window.location.pathname;
    console.log(path);
    // ...
  });
  Home($app, handleClick);
})();
```

코드를 작성하고 결과를 확인해보고 싶으면 다음과 같은 명령어를 입력하면 된다.

```PS
parcel public/index.html
```

명령어를 입력하면 개발 서버가 실행되고 프로젝트 폴더 내 dist 폴더에 번들링된 HTML과 자바스크립트 파일을 확인할 수 있다. http://localhost:1234에 접속하여 코딩 결과물도 확인할 수 있다. 포트번호가 마음에 들지 않으면 -p [포트 번호]로 포트 번호를 지정할 수 있다.

.parcelrc 파일을 생성 후 설정함으로써 Parcel의 기본 설정을 확장시킬 수 있다.

```JSON
{
  "extends": ["@parcel/config-default", "타 플러그인"]
}
```

## 중간 정리

Browser API 학습 용도로 우연히 사용하게 된 거라 개인 프로젝트에 적용할 계획은 없다. 문서를 다 읽지 않아서 아직도 Parcel에 대해 모르는 지식이 많다. 공식문서에는 리액트 개발 환경도 어떻게 설정해야 하는지 적혀있어서 추후에 리액트 작은 프로젝트를 할 때 한번 적용해 볼 생각이다.

[영어 공식문서](https://parceljs.org/)가 더 내용이 알찬 편이다. 플러그인 시스템에 대해서는 언젠가 깊게 학습해 볼 필요가 있을 것 같다. 공식문서에서 플러그인 시스템에 대한 대략적인 구조를 파악할 수 있었다.

![Parcel Plugin System](https://static.nwlee.app/public/L0HD8G00/0212a382-48fb-4464-8db5-67f5b0ea9d8d.png)

Parcel은 여러 개의 단계에 걸쳐 실행된다.

1. **Resolving**과 **Transforming** 단계에서는 모든 애셋 파일의 그래프가 생성된다. Parcel에서 애셋 파일은 각 입력 파일을 의미하고, 주로 HTML 또는 자바스크립트 파일이 입력 파일이 된다.

2. 애셋 그래프는 **Bundling** 단계에서 번들로 변환되고, 번들링된 파일들의 이름은 이후 Naming 단계에서 정의된다.

3. **Packaging** 단계는 HTML, 자바스크립트와 같은 번들링된 애셋 파일들을 하나의 파일로 병합하는 과정이다.

4. **Optimizing** 단계는 각 번들 파일의 내용물을 terser나 htmlnano 등으로 변환하는 과정이다. 이 단계가 종료되면 각 번들 파일의 해시를 결정하고 이는 곧 파일 이름에 반영된다. (이게 아마 Naming 과정으로 보인다.)

5. **Compressing** 단계에서는 인코딩된 각각 아웃풋 파일을 파일 시스템에 저장한다.
