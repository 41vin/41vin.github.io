---
layout: post
title: AWS Open RAN whitepaper
description: >
  AWS에서 발간한 Open RAN 아키텍쳐 white paper 내용 정리입니다.
image: /assets/img/blog/k8s_logo.png
sitemap: false
---

# O-RAN architecture

### 컴포넌트
O-CU, O-DU, O-RU는 3GPP specification을 따르고 있습니다. 이들은 E2와 O1 인터페이스를 통해 RAN management와 AI/ML-powered RAN optimization을 가능하게 합니다. 이와 더불어 O-RAN은 개방적이고 상호운용 가능한 fronthaul interface를 이용해 O-DU와 O-RU 사이를 연결합니다. 

CU, DU, RU 말고 다른 아키텍쳐들도 존재합니다.

1. O-Cloud: 
- 서버 하드웨어와 네트워크를 포함합니다. O-RAN의 function들이 작동하는 fundamental layer


2. The Service Management and Orchestration(SMO) framework
- 다양한 종류의 management service들을 묶어놓은 것입니다. Operator의 네트워크에서 SMO는 RAN Management 뿐 아니라 Core network management, transport management, end-to-end network slice management들과 같은 서비스들을 제공함
- Open Network Automation Platform(ONAP)가 SMO의 한 종류


3. RIC(RAN Intelligent Controller)
- O-RAN에서 처음 소개한 컴포넌트
- Radio 리소스들을 제어하고 mobility management, admission control, interference management등을 이용하여 RAN의 성능을 향상시킴
- 데이터 분석, AI/ML training을 이용해 RAN 최적화를 실현함
- RIC은 SMO 서비스들을 데이터 수집이나 O-RAN 컴포넌트들을 provisioning할 때 사용함
  
### 인터페이스
- E1: 