---
title: 이력서
description:
date: '2021-12-31'
aliases:
  - about-us
  - about-hugo
  - contact
license: CC BY-NC-ND
lastmod: '2021-12-31'
menu:
  main:
    weight: -90
    pre: user
---

## 소개

- 이름: 이노원
- 깃허브 저장소: [https://github.com/nw-lee](https://github.com/nw-lee)
- 블로그: [https://nwlee.app](https://nwlee.app)

## 현재 기술 스택

- 언어: Typescript, Kotlin
- 도구: Visual Studio Code, Git, Github, Android Studio
  - 프론트엔드: React, Next JS, Tailwind CSS,
  - 안드로이드: Navigation, Safe Args, Retrofit2, Coroutine, Hilt
  - 기타: AWS Lambda, Serverless Framework

## 프로젝트 목록

1. Koreanify

한국 내 여행지를 나열 및 소개해주는 안드로이드 애플리케이션

- 제작 동기

  - 부스트캠프에서 배운 코틀린, 안드로이드 개발 지식을 복습하고 개인 프로젝트에 적용하기 위함
  - 공공데이터포털에서 제공하는 한국관광공사 영문 관광정보 API로 영어권 관광객 대상 여행 정보 애플리케이션을 만들어 보기로 결심

- 프로젝트 기간

  - 2021년 12월 13일 ~ 현재

- 핵심 기술

  - Kotlin, Retrofit2, Gson, Hilt, Coroutine, Shared Preference

- 내용

  - 리사이클러 뷰에서 목록 끝에 도달하면 새로운 데이터를 뷰모델에서 요청 및 업데이트 (무한 스크롤)
  - 목록 화면에서 항목을 터치하여 상세 화면으로 이동할 때 스크롤 상태 유지. 이미지 깜빡임을 최소화하기 위해 리사이클러 뷰 캐시 설정
  - Shared Preference에 정렬 방식 저장, 앱을 종료 후 다시 실행해도 정렬 방식을 유지
  - 상세 화면에서 여행지 상세 정보와 여러 개의 여행지 이미지를 하나의 리사이클러 뷰에 렌더링
    - 어댑터에서 뷰 타입을 구분할 때 else일 때의 코드 없이 when 문을 작성
    - 공통되는 봉인 클래스를 하나 정의한 다음 여행지 정보, 여행지 이미지 클래스가 해당 봉인 클래스를 상속

- 링크
  - [구글 플레이](https://play.google.com/store/apps/details?id=com.nwlee.koreanify)

2. Travelog

여행지를 추천받거나 직접 검색하여 일정을 계획하고 여행 경험을 기록할 수 있는 안드로이드 애플리케이션

- 제작 동기

  - 여행 계획을 체계적으로 수립
  - 어디로 여행을 갈지 모르겠을때 여행지 추천
  - 여행지에 대한 상세 정보를 확인
  - 촬영한 사진과 감상문을 나만의 핸드폰에 저장
  - PDF 파일 형태로 문서화하여 친구들과 공유

- 프로젝트 기간

  - 2021년 10월 25일 ~ 2021년 12월 02일

- 핵심 기술

  - Kotlin, Retrofit2, Gson, Hilt

- 내용(맡은 역할)

  - HTTP 통신을 통한 외부 API 호출 및 데이터 가공
  - 비동기 작업에서 발생할 수 있는 예외에 대비할 수 있는 뷰모델 정의하고 각 뷰모델이 상속받도록 함
  - RecyclerView와 ListAdapter로 목록 형태의 데이터를 렌더링
  - Hilt로 뷰모델, 프래그먼트에 의존성 주입
  - 선택한 관광지를 지도를 통해 위치를 시각적으로 표현, 지도 화면 출력할 때 메모리 누수 점검

- 링크
  - [깃허브 저장소](https://github.com/nw-lee/android10-Travelog)

3. Too Much News

- 제작 동기
  - 사회에도 관심을 가지고 싶은데, 언론사가 너무 많아서 어떤 자료를 봐야 할지 모르는 상태.
  - 국외에서 제공하는 뉴스 Open API를 사용하여 다양한 정보를 골고루 받을 수 있는 웹 사이트 제작.
  - API 테스트 결과 여러가지 언론사에서 정보를 받아오는 것을 확인
  - 산업, 경제, 과학, 건강과 같은 다양한 카테고리 존재
- 프로젝트 기간
  - 11월 27일 ~ 현재
- 핵심 기술
  - NextJS, Typescript, AWS Lambda, Serverless Framework
- 내용
  - AWS Lambda에서 1시간마다 외부 API 호출하여 데이터를 수집
  - 브라우저에서는 Lambda로 만들어진 API에서 수집된 데이터를 호출
  - API에서 가져오는 데이터와 현재 출력되는 데이터가 달라지면 최신 데이터로 교체
  - 각 이미지를 클릭하면 데이터를 로컬 스토리지에 저장한 다음 상세 페이지로 이동
  - 상세 페이지에서 로컬 스토리지에 데이터가 있는지 검사
  - 이미지를 그리드 형태로 배치하거나 마우스 스크롤이 올라가면 기사 제목이 표시되게 하는 등의 간단한 이벤트 처리
- 참고 링크

  - [웹 사이트](https://nostalgic-jepsen-e4b338.netlify.app)
  - [깃허브 저장소](https://github.com/nw-lee/too-much-news)

4. Banchan App: 반찬 웹 어플리케이션

- 프로젝트 기간
  - 10월 24일 ~ 현재
- 제작 동기
  - 식당이 없는 기숙사에 거주하면서 식사를 비싼 편의점, 배달 음식으로 해결.
  - 사용자가 구매하고 싶은 반찬을 한 눈에 보고 비교.
  - 상품 제휴 링크와 연결시켜 부수적인 수익 기대.
- 사용 기술
  - Typescript, Next JS, Tailwind CSS
  - Go, Serverless Framework, PostgreSQL
- 내용
  - 스크롤이 끝에 도달했을 때를 감지하여 API를 호출함으로써 일정한 개수의 데이터를 등록일 내림차순으로 렌더링
  - 정기적으로 요금을 지불해야 하는 가상 머신과 다르게 사용자의 접속 횟수에 따라 요금이 지불되는 서버리스 환경의 API 구축
  - 서버리스 프레임워크에서 제공하는 명령어로 복잡한 배포 과정 없이 API를 배포
- 참고 링크
  - [웹 사이트 주소](https://banchanapp.com)
  - [백엔드 깃허브 저장소](https://github.com/nw-lee/banchan-serverless-api)
  - [프론트엔드 깃허브 저장소](https://github.com/nw-lee/banchan-next)

5. 과학 뉴스 모음

- 제작 동기
  - 과학 지식과 연구 소식에 대한 정보를 편하게 볼 수 있는 방법을 찾음
  - 사람들에게 과학, 기술에 대한 정보를 더 쉽게 전달하기 위해 과학 뉴스 전용 웹 사이트 제작
- 프로젝트 기간
  - 04월 20일 ~ 06월 20일
- 사용 기술
  - React, Typescript
  - Express, Prisma, PostgreSQL
- 내용
  - 뉴스 RSS 데이터 수집 및 가공
  - 신문사마다 다른 데이터 구조에 대한 분기 처리
  - HTTP 요청 및 데이터베이스 저장과 같은 비동기 작업에서 발생할 수 있는 에러 처리
  - 적은 사용자수, 서버 운영 비용이 정기적으로 납부되어 현재 서버를 삭제한 상태
- 첨부 이미지
  <p align="center">
    <img src="https://static.nwlee.app/public/KVIR6YO0/1dabf146-b712-480b-9362-597d2218cb3d.jpeg" width="180" />
    <img src="https://static.nwlee.app/public/KVIR6YO0/0802d2e6-433a-42e0-9133-1324a5f7836c.jpeg" width="180" />
  </p>

## 교육 이수 사항

1. 부스트캠프 웹 & 모바일 6기 챌린지

- 교육기관: 네이버 커넥트재단
- 교육기간: 2021년 07월 19일 ~ 2021년 08월 13일
- 교육내용
  - Kotlin 언어 기초 문법
  - Coroutine을 통한 비동기 처리
  - 객체지향 프로그래밍
  - 메모리 영역의 자료구조
  - 네트워크 HTTP 통신

2. 부스트캠프 웹 & 모바일 6기 멤버십

- 교육기관: 네이버 커넥트재단
- 교육기간: 2021년 08월 23일 ~ 2021년 12월 03일
- 교육내용
  - 코틀린 안드로이드 프로그래밍 학습
  - 매주 주어지는 과제 분석 및 문제 해결 과정 공유
  - 안드로이드 디자인 패턴
  - 안드로이드 그룹 프로젝트

## 자기소개서

업데이트 중입니다.

## 학력 사항

2016년 03월 ~ 2022년 02월 단국대학교 응용컴퓨터공학과 졸업
