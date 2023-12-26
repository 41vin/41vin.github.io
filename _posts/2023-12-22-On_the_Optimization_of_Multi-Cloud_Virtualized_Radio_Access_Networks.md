---
category: vRAN
tags: [paper]
usemath: [latex, ascii]
---

# Introduction

![image-20231222141559887](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-22-On_the_Optimization_of_Multi-Cloud_Virtualized_Radio_Access_Networks/image-20231222141559887.png)

# Preliminaries and Model

## Background

3GPP는 8개의 서로 다른 splits을 제시하였지만 아래에 소개되어있는 3개의 split들이 주로 쓰이고 있다.

- Split 1 (S1; PDCP-RLC): 
  - CU: RRC, PDCP, 그리고 upper layer들 
  - DU: PHY
- Split 2 (S2; MAC-PHY):
  - CU: MAC과 upper layer들
  - DU: PHY와 RF layer들
- Split 3 (S3; PHY-RF):
  - CU: RF 제외 모든 function들



S1에서 S3로 갈수록 더 centralized가 되어 cost saving이 가능해지고 performance가 좋아짐.

$$\Leftrightarrow$$

Centralizing을 하게되면 CU에 전송되는 data의 volume이 커지고, 요구 delay가 더 tight 해짐



## Network

BS는 $$f_0 \rightarrow f_1 \rightarrow f_2 \rightarrow f_3$$ 순으로 함수를 실행함.

1. $$f_0$$: RF-related operations
2. $$f_1$$: PHY (CU와 DU 둘 다 deploy 가능)
3. $$f_2$$: RLC and MAC (CU와 DU 둘 다 deploy 가능)

- DU의 link 공유:
  - DU들이 link를 공유한다는 것은 여러 DU들이 하나의 네트워크 인프라를 사용해 데이터를 주고받는다. 이는 네트워크 자원을 더 효율적으로 사용하고, 각 DU에 대해 별도의 점대점 연결을 구축하는 것보다 비용을 절감할 수 있다.
  - UE와 가까운 위치에서 데이터 전송 효율성을 높인다.
- CU의 고용랑 링크 사용:
  - CU가 네트워크 코어에 고용랑 리크를 통해 직접 연결된다는 것은 중앙 처리 장치가 매우 큰 데이터 양을 처리하고 네트워크의 다른 부분으로 빠르게 전송할 수 있음을 의미한다.
  - 중앙 처리에 있어서 효율성과 빠른 데이터 전송을 보장한다.



## Demand & Cost

본 논문은 Uplink 환경에 집중하였다.

### Demand

- 각 DU-n에 의해 서비스되는 사용자들은 Random process를 통해 데이터 request를 생성하게 된다. 이 request는 Upper bound($$\phi_n$$)에 의해 제한된다.
- 또한 request들은 data flow($$\lambda_n$$)을 생성하게 되는데, 이 때 $$\lambda$$는 request 당 byte 수를 의미한다.

### Cost

- CU에서 function을 실행하는 것은 cost-effective하다.

- 벡터 $$\alpha$$와 $$\beta$$

  - $$\alpha_m$$: CU-m에서 가상 머신(VM)을 컴퓨팅 할 때 드는 비용

  - $$\beta_m$$:  CU-m에서 컴퓨팅과 가상 머신(VM)을 인스턴스화 할 때 드는 비용

- $$\zeta_k$$: 각 path $$p_k$$에서 Routing하는데 드는 비용, link가 제 3자에 의해 임대되는 경우 발생할 수도 있고, 이를 이용해 **네트워크의 운영 비용**을 모델링할 수 있다.



### Problem Definition

#### Objective

User 트래픽을 서비스하며 Network operation cost를 최소화하는데 아래의 조건들을 최적화하여야 한다.

- Deployment: 사용 가능한 CU의 위치 중 어느 것을 사용해야 하는가
- Assignment: 각 DU를 어떤 CU가 서비스해야 하는가
- Placement: 각 BS에 대해 $$f_1, f_2, f_3$$을 어디에 배치해야 하는가
- Routing: DU에서 CU로 데이터를 어떻게 라우팅해야 하는가

위의 조건들은 interesting trade-off들을 발생시킨다.

1. DU에 function들을 놓는 것은 routing 비용을 감소시키지만 computing cost를 증가시킨다.
2. Function들을 centralizing하는 것은 computing cost들을 감소시키지만 routing cost를 증가시킨다.

이외 여러가지 trade-off들이 발생하게 된다.

---

# Problem Formulation

## Function Placement

1. Function deployment 결정
   - $$x_{1n}, x_{2n}$$: $$f_1, f_2$$를 DU-n에 배치할 것인지 여부를 나타내는 decision variable
   - $$y_{1nm}, y_{2nm}$$: function을 CU-m에 배치할 것인지 여부를 나타내는 decision variable
   - $$x_1, x_2$$: 모든 DU에 대한 $$f_1, f_2$$의 function depoyment vector를 나타낸다.
   - 







