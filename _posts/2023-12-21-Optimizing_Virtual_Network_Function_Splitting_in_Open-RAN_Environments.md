---
category: O-RAN
tags: [paper]
usemath: [latex, ascii]
---

# Optimizing Virtual Network Function Splitting in Open-RAN Environments

![차세대 5G 기술로 떠오른 '오픈랜', 영국·구글 등 참여로 개발 가속화…통신장비 시장 판도 바꿀까 - 녹색경제신문](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/290498_310007_5513.jpeg)

## Problem Formulation

Placing network functions in DUs reduces bandwidth usage of midhaul links, while placements in CUs is more cost-effective due to sharing computing resources among all RUs.

이 논문에서는 Split point를 결정하는 것을 Optimization Problem으로 설정하였다. Objective는 CU들간, midhaul들간의 load를 조절하는 것이다.

1. Midhaul 링크에 여유 bandwidth를 유지하는 것을 목표로 함
2. 서버가 과부하되는 것을 방지함

![image-20231221171742667](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221171742667.png)



### A. Network Constraints

#### Constraint (1)

- 각 RU-m에 하나의 split만을 제한한다.

![image-20231221172156873](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221172156873.png)



#### Constraint (2)

- RU의 전체 computational load의 합은 속해있는 DU-n의 processing capacity를 넘을 수 없다.

  ![image-20231221173009477](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221173009477.png)

  - $$\rho_s^d$$: DU-n에 속해있는 RU-m의 computational load
  - $$\lambda_m$$: RU-m으로 부터 core node에 가는 aggregate된 traffic flow  



#### Constraint (3)

- RU의 전체 computational load의 합은 속해있는 CU-q의 processing capacity를 넘을 수 없다.

  ![image-20231221173117813](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221173117813.png)

  ![image-20231221173727252](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221173727252.png)

  - $$\rho_s^c$$: Split s에 속해있는 CU의 computational load
  - $$\lambda_m$$: RU-m으로부터 core node에 가는 aggregate된 traffic flow
  - $$\alpha$$: CU의 maximum rate of load
  - $$\varphi_{mc}$$: RU-m이 CU-q에 속해있는지를 나타내주는 binary variable



#### Constraint (4)

- RU-m으로 부터 core node에 aggregated traffic flow를 보내기 위해선, RU-m부터 CU-q까지 오직 하나의 path만 선택해야 한다.

  ![image-20231221173926726](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221173926726.png)

  - $$\phi_{mq}^p$$:  Path p가 RU-m과 CU-q 연결 통로로 정해졌는지를 나타내는 binary variable

  

- $$r_m$$ : RU-m과 CU-q 연결에 요구되는 bandwidth

![image-20231221174703982](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221174703982.png)



- Routing Decision 문제는 link capacities와 관련되어 결정된다.

![image-20231221174802684](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221174802684.png)

![image-20231221174812415](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-OrchestRAN/image-20231221174812415.png)

- $$\beta$$ : Maximum utilization rate of midhaul links and performs load balancing between midhaul links.
- $$I_{p}^{ij}$$ : path p가 link (i,j)를 포함하는지 나타내는 binary variable



#### Constraint (5)

![image-20231222101100855](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-Optimizing_Virtual_Network_Function_Splitting_in_Open-RAN_Environments/image-20231222101100855.png)

- $$d_{mq}^p$$: 선택한 path($$P_{mq}$$)의 delay



### B. Objective

Midhaul 링크와 CU 사이의 load balace 문제 정의

![image-20231222101347075](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-Optimizing_Virtual_Network_Function_Splitting_in_Open-RAN_Environments/image-20231222101347075.png)



### C. Complexity Analysis

풀고자 하는 문제: Customers in **c**apacitated **f**acility **l**ocation **p**roblem (CFLP) &rarr; NP-hard problem

---

## Proposed Heuristic Solution

![image-20231222102803359](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-Optimizing_Virtual_Network_Function_Splitting_in_Open-RAN_Environments/image-20231222102803359.png)

1. 모든 RU에 대한 초기 설정:
   - 모든 RU에 대해 'split S3'을 설정한다.
2. 각 DU-n에 대한 처리:
   - 각 DU-n에 연결된 RUs 집합인 $$S_n$$을 $$\lambda_m$$의 내림차순으로 정렬한다.
     - $$\lambda_m$$: RU-m의 bandwidth 요구량
3. 네트워크 기능 배치:
   - 'f'는 network function을 의미함. 'f'를 1부터 3까지 순차적으로 증가시키면서 다음 작업을 수행
     - $$S_n$$에 있는 각 RU-m에 대해, 만약 RU-m의 기능 'f'에 대한 bandwidth 요구량($$\lambda_{m \rho f}$$이 DU-n의 남은 처리 용량($$H_n$$)을 해당 요구량($$\lambda_{m \rho f}$$)만큼 감소시킨다.
     - RU-m의 'split' 설정을 업데이트한다.
   - 만약 RU-m의 요구량이 남은 capacity를 초과한다면, RU-m을 $$S_n$$에서 제거한다.
4. 반복:
   - f값이 3에 도달할 때까지 위의 과정을 반복한다. 즉, 모든 네트워크 기능 f1, f2, f3에 대해 이러한 배치를 시도한다.

> DU의 처리 능력을 최대한 활용한다는 것이 Algorithm 1의 핵심





![image-20231222104730481](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/2023-12-21-Optimizing_Virtual_Network_Function_Splitting_in_Open-RAN_Environments/image-20231222104730481.png)

1. Data setting:
   - $$S$$: 모든 RU의 집합
   - $$Q$$: 모든 CU의 집합
   - $$L$$: 모든 링크의 집합
   - $$R_q$$: 각 CU-q에 연결된 RU들의 집합
   - $$B_m$$: 각 RU-m에 대해 가능한 CU 경로의 집합
   - $$T_l$$: 각 링크 l이 사용하는 RU들의 집합
2. 알고리즘 실행:

 	1. RU 선택과 배치 확인:
     - 먼저 각 RU를 확인하여, 단 하나의 CU에만 연결될 수 있는 RU가 있으면, 그 RU를 해당 CU에 배치하고, $$S$$ 집합에서 제거한다.
 	2. 계산 부하가 가장 높은 RU 찾기:
     - 모든 RU들중에서 $$\lambda_{m \rho s}$$가 가장 높은 RU를 찾는다. 이 RU의 배치 우선순위를 가장 높게 설정한다.
 	3. 가능한 CU와 경로 선택:
     - 해당 RU에 대해 가능한 모든 CU를 검토하고, 각 CU에 대해 계산 부하 대비 available resource($$\alpha_{q}$$)가 가장 높은 CU를 찾는다.
     - 또한 해당 RU에서 각 CU로 가는 경로 중에서 bottleneck link의 free capacity가 가장 큰 path를 선택한다.
 	4. 최적의 CU 선택:
     - 계산된 $$\alpha_{q}$$와 bottleneck link의 bandwidth requirement($$\beta_{q}$$)를 합한 값이 가장 작은 CU를 찾아서, 그 CU에 해당 RU의 network function을 배치한다.
 	5. 결과 업데이트:
     - 배치된 RU를 $$S$$에서 제거하고, 해당 RU가 사용하는 링크의 집합($$T_l$$)과 CU의 RU 집합($$R_q$$)을 업데이트한다.

**이 모든 단계들을 모든 RU가 배치될 때까지 반복한다.**
