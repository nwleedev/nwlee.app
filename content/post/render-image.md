---
title: 'Render Image'
description: 'Image Rendering Test in Hugo Blog Application'
date: 2021-10-17T23:42:07+09:00
image:
math: false
license:
hidden: false
comments: true
draft: false
---

# Image Rendering

외부 이미지 렌더링 테스트

<!-- <img src="https://static.nwlee.app/public/f30b7b88-d7eb-43c4-a7cd-73627f67d65f.png" loading="lazy"> -->
<img src="https://static.nwlee.app/public/KVYH1C00/71f49346-12b3-4d54-a669-d000f07828b6.jpeg" loading="lazy" >

## 대략적인 과정

1. AWS Certificate Manager(ACM)에서 SSL 인증서 발급을 신청한다.
2. 신청 후 주어지는 레코드 키, 값 쌍을 CNAME 레코드로써 DNS 레코드 목록에 추가해준다.

- GoDaddy DNS를 사용한다면 키 문자열에서 최상위 도메인을 마침표까지 포함하여 제거해줘야 한다.
- [관련 AWS 문서](https://docs.aws.amazon.com/ko_kr/acm/latest/userguide/troubleshooting-DNS-validation.html#troubleshooting-DNS-GoDaddy)

3. 몇 분 지나서 ACM 콘솔에 접속하면 인증서가 발급되었다는 메시지를 볼 수 있다.
4. S3 콘솔에서 모든 퍼블릭 액세스를 차단한다.
5. CloudFront에서 S3 배포 설정을 해준다.

- 이 때 CNAME, 대체 도메인 주소 입력 폼에 서브도메인을 입력한다.
- 사용자 정의 인증서에서 발급받은 인증서를 선택한다.

6. CloudFront 배포를 생성한다.

## 후기

S3 버킷 내 이미지를 클라우드프론트에서 배포하도록 설정했을 때가 S3 객체 URL로 제공할 때보다 불러올 때 걸리는 시간이 더 적게 걸린다. 그리고 클라우드프론트는 HTTP2도 지원하기 때문에 이미지를 불러오느라 다른 요소들을 불러오지 못하는 문제점도 사라졌다. S3 버킷에 대한 직접적인 접근을 막을 수 있는 것도 장점이다.

추후에 과정을 더 상세하게 작성할 계획이다.
