---
layout: post
title: Hosting AI/ML Workflows on O-RAN RIC Platform
description: >
  Samsung Research에서 나온 논문을 정리한 글입니다.
image: /assets/img/blog/o-ran.png
sitemap: false
---


본 글은 OAIC의 공식문서를 참고하여 작성하였습니다.
{:.note}

RIC 테스트베드를 구성하고 있는 프로그램들의 버전은 다음과 같습니다.
- Helm: v.3.2.3
- Kubernetes: v1.16.0
- CNI: v0.7.5

이 중 Kubernetes의 버전이 너무 오래되어 apt 저장소에서 누락되었습니다.
따라서 binary 파일을 직접 다운받아 Kubernetes 환경을 구성하였습니다.  

