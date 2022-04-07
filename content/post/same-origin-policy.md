---
title: 'SOP & CORS'
description: '동일 출처 정책, 그리고 교차 출처 자원 공유에 대해 알아보았습니다.'
date: 2022-04-07T16:37:49+09:00
image: 'https://static.nwlee.com/public/L1O8G000/b772e7f2-e016-4762-839c-f74f5aa984e6.png'
math:
license:
hidden: false
comments: true
draft: true
tags:
  [
    '웹 보안',
    'Web Security',
    'CORS',
    'SOP',
    '동일 출처 정책',
    '교차 출처 리소스 공유',
  ]
categories: ['웹 보안']
---

# Same Origin Policy & Cross Origin Resource Sharing

## 이 글을 작성하는 이유

외부 API를 불러올 때 프론트엔드에서 데이터를 불러올 수 없는 경우가 여러 번 있었다.

1. 웹 사이트를 배포하고 HTTPS 인증서를 적용했을 때 호출하는 API가 HTTP인 경우 Mixed Content 메시지를 띄우며 에러가 발생한다.

- 최신 브라우저는 암호화되지 않은 HTTP 프로토콜로 요청했을 때의 응답 데이터를 차단하기 때문이다.

2. 해당 API가 CORS를 지원하지 않았기 때문에 웹 브라우저에 우리가 원하는 데이터를 받을 수 없었다.
3. 브라우저에서 API를 바로 호출하게 되면 API 키가 노출될 수 있다.

CORS는 브라우저에 적용되고 그 이외에서의 요청은 적용되지 않기 때문에 작은 프록시 서버를 만들어서 요청을 보내도록 했다. API 키를 외부에서 볼 수 없도록 서버에 숨길 수 있었다.

이 글을 작성하면서 SOP, CORS가 뭔지 조금 더 배울 수 있었다. 또한 브라우저에서 인증 정보가 담겼을 때 어떻게 요청을 보내고 서버에서는 어떻게 응답 헤더를 설정해야 하는지 조금이나마 이해할 수 있었다.

## SOP

SOP는 Same Origin Policy, 같은 출처의 자원만 가져오도록 하는 정책이다. 출처는 오리진이라고 부르며 웹 사이트에 접근할 때 사용하는 URL의 프로토콜, 도메인, 포트로 정의된다. 두 주소의 프로토콜, 도메인, 포트가 전부 일치할 경우 오리진이 같다고 할 수 있다.

예를 들어 `https://example.com`과 `https://example.com:8000`은 오리진이 다르다고 할 수 있다. 프로토콜, 도메인이 같지만 포트가 다르기 때문이다. https 프로토콜의 경우 일반적으로 443 포트를 사용한다.

| URL                                             | 결과   | 이유              |
| ----------------------------------------------- | ------ | ----------------- |
| http://store.example.com/dir/other.html         | 같다   | 경로만 다르다     |
| http://store.example.com/dir/inner/another.html | 같다   | 경로만 다르다     |
| https://store.example.com/page.html             | 다르다 | 프로토콜이 다르다 |
| http://store.example.com:81/dir/page.html       | 다르다 | 포트가 다르다     |
| http://news.example.com/dir/page.html           | 다르다 | 도메인이 다르다   |

SOP는 하나의 오리진에 존재하는 문서나 스크립트가 다른 오리진과 상호작용하는 것을 차단해준다.

예를 들어 악의적인 사이트가 iframe을 통해 사용자가 자주 사용하는 사이트를 출력해 줄 경우, 사용자가 해당 사이트에 로그인 요청을 보내면 로그인 요청에 담긴 데이터가 탈취될 위험이 존재한다. 또한 탈취한 데이터를 가지고 사용자가 의도하지 않은 요청을 보낼 수도 있다.

오리진이 다름에도 자원을 요청할 수 있는 경우가 존재한다.

- CSS 파일: Content-Type이 명시되어 있으면 link 태그나 @import 문법으로 불러올 수 있다.
- 멀티미디어 파일: 음성 및 동영상 파일이 있다.
- 이미지 파일: img 태그에 이미지 주소를 임베딩할 수는 있지만, 자바스크립트 캔버스를 통해 불러오는 것은 불가능하다.
- iframe 태그: 해당하는 리소스에 X-Frame-Options 옵션이 정의되어 있으면 iframe 태그로 임베딩할 수 있다. 자바스크립트로는 불러올 수 없다.

## CORS

Cross Origin Resource Sharing은 다른 오리진 간 자원의 접근을 허용하는 방법이다. 이는 타 도메인 간 요청을 차단하는 SOP 정책을 보완해주는 역할을 한다. 하지만 모든 도메인에 CORS를 허용하는 등 잘못 설계할 경우 취약점이 생길 수 있다.

HTTP 응답 헤더에 Access-Control-Allow-Origin을 설정함으로써 타 도메인으로부터 요청을 허용할지 설정할 수 있다.

- `Access-Control-Allow-Origin: https://example.com`

이 경우는 `https://example.com`에서만 자원을 요청할 수 있다.

- `Access-Control-Allow-Origin: *`

이 경우는 모든 오리진에서 자원을 요청할 수 있다.

CORS 정책의 경우 브라우저에 명시되어 있다. 즉 브라우저가 아닌 서버 간 통신할 때에는 이 정책을 사용할 수 없다.

### Preflight 요청

최신 웹 브라우저에서는 다른 도메인의 리소스에 직접 요청하기 전 preflight 요청을 먼저 보낸다. OPTIONS 메소드로 우선 요청을 보내서 안전하게 전송할 수 있는지 파악한다.

프리플라이트 요청으로 받은 응답 내 헤더를 통해 서버는 어떤 메소드와 헤더를 사용할 수 있는지 프론트엔드에 가르쳐준다.

- Access-Control-Allow-Origin

사용할 수 있는 오리진, 해당 오리진에서 요청을 보낼 수 있다는 의미이다.

- Access-Control-Allow-Methods

사용할 수 있는 REST 메소드를 알 수 있다.

- Access-Control-Allow-Headers

사용할 수 있는 헤더를 알 수 있다.

### HTTP 응답 헤더

이 외에도

- Access-Control-Expose-Headers

브라우저가 사용할 수 있는 헤더를 서버에 추가하는 역할을 한다.

- Access-Control-Max-Age

프리플라이트 요청을 얼만큼 캐시할 수 있는지에 대한 시간을 나타낸다.

- Access-Control-Allow-Credentials

프론트엔드에서 보내는 요청에 자격 증명이 설정되었을 때, 해당 속성이 true일 경우에만 브라우저는 응답받은 데이터를 자바스크립트에 넘겨줄 수 있다. 자격 증명은 HTTP Authorization 헤더, 쿠키, TLS 인증서가 있다. Fetch API의 경우 credentials 속성과 함께 작동한다.

```HTML
<script>
  fetch("https://api.example.com", {
    // ...
    credentials: "include"
  })
</script>
```

credentials 속성에 include일 경우 요청에 인증 정보를 포함시키겠다는 의미이다. 서버 응답 헤더가 `Access-Control-Allow-Origin: *`일 경우 경고 메시지가 출력된다.
