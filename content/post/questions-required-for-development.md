---
title: '개발에 필요한 학습 내용 정리'
description:
date: 2022-01-04T01:02:47+09:00
image: 'https://static.nwlee.app/public/KXXX1XC0/10cbb09a-629f-4a1b-9401-cd8f59a98d3a.jpeg'
math:
license:
hidden: false
comments: true
draft: true
tags: ['Study', 'Learning']
categories: ['Study']
---

# 학습 내용 정리

## 동적 프로그래밍

복잡한 문제를 여러 개의 보다 쉬운 문제로 나누어 해결하는 방법이다. 반복적으로 계산되는 값들을 배열 등 자료구조에 저장해뒀다가 필요한 경우에 재사용하는 방법이다. 같은 문제를 마주쳤을 때 저장한 값을 사용함으로써 연산 시간을 줄일 수 있다.

## 가비지 콜렉션

프로그램이 동적으로 할당한 메모리 영역에서 더 이상 사용할 수 없는, 다시 말해 어떤 변수도 참조하지 않는 영역을 해제하는 메모리 관리 기법이다. 프로그래머가 메모리 영역을 관리할 필요성이 줄어드는 장점이 있다.

## 이미지 파일 형식의 차이점

JPG, JPEG

- 손실 압축 알고리즘을 사용한다. 파일 압축률이 높아 파일 크기를 줄일 수 있다. 이미지가 부자연스럽게 보일 수 있다.

PNG

- 투명한 배경을 지원한다. 비손실 압축이다. JPEG에 비해 용량이 큰 편이다.

## 가상 메모리

주기억장치보다 더 많은 메모리 영역을 제공하기 위해 각 프로그램에 실제 메모리 주소가 아닌 가상의 메모리 주소를 할당하는 메모리 관리 기법이다. 가상 메모리의 주소를 가상 주소, 실제 메모리의 주소를 물리적 주소라고 부른다. 가상 주소는 메모리 관리 유닛에 의해 물리적 주소로 변경될 수 있다.  
프로그램은 프로세스들이 공유하고 있는 메모리를 관리할 필요가 줄어들며, 실제 메모리보다 더 많은 메모리 공간을 사용할 수 있다.

## 비대칭 암호화

암호화, 복호화에 필요한 키가 서로 다른 암호화 방식이다. 자신만의 개인키를 바탕으로 공개키가 생성된다. 공개키는 개인키와 다르게 공유될 수 있다. 공개키로 암호화한 데이터는 개인키로 복호화할 수 있으며 반대로 개인키로 암호화한 데이터는 공개키로 복호화할 수 있다.

## Git

Git은 프로젝트 내 파일의 변화를 추적하여 개발자로 하여금 파일의 변경사항들을 쉽게 알 수 있게 해준다. 협업 중 각자 작업한 파일들을 병합시켜주기 때문에 프로젝트 파일의 충돌을 막아준다. Git은 로컬 컴퓨터에서 작동하고, 프로젝트 파일들의 변경사항들도 개인 컴퓨터에 기록된다. Github 등의 온라인 서비스를 통해 자신의 프로젝트를 빠르게 배포하고 타인의 오픈소스 프로젝트에도 참가할 수 있다.

## Cache

값을 미리 복사해 저장해놓는 임시 장소를 의미한다. 원본 데이터를 접근하는데 시간이 오래 걸릴 경우 캐시에 저장된 데이터를 사용할 수 있다. 이로써 데이터에 접근하는데 시간을 줄일 수 있다. 안드로이드에서는 SharedPreference와 SaveInstanceState(Bundle)가 캐시에 해당한다. SharedPreference는 오랜 기간동안 데이터를 보관해야 할 때 사용한다. 반대로 Bundle은 앱이 실행될 동안 데이터를 잠깐 저장하는 용도로 사용된다.