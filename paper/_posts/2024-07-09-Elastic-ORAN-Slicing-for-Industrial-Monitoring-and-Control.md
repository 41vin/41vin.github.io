---
layout: post
title: Elastic O-RAN Slicing for Industrial Monitoring and Control - A Distributed Matching Game and Deep Reinforcement Learning Approach
tags: [ieee]
image: /assets/img/blog/elastic-o-ran/Title.png
description: >
  **Authors**: Sarder Fakhrul Abedin et al.
sitemap: false
hide_last_modified: true
---

Industrial IoT, open RAN slicing, age of information, game theory, deep reinforcement learning
{:.note title="Keywords"}


* this unordered seed list will be replaced by the toc
{:toc}



# 1. Why this paper
- Slicing 관련 배경지식 필요
- IIoT를 기반으로 한 RAN Slicing 논문 작성 예정

---

# 2. Summary of paper

## 2.1 Introduction

- QoS Requirements for industrial use-cases

## 2.3 System Model

![Figure1](/assets/img/blog/elastic-o-ran/Figure1.png)

### 2.3.A Traditional RAN to O-RAN in IIoT
- O-RAN management layer
  - 주요 RAN 최적화 솔루션이 작동
  - Network operator의 소유

- O-RAN control layer
  - Network operator에게 연결성과 QoS 관리 서비스를 제공 

### 2.3.B QoS Model

#### 1. Transmission Capacity and Energy Consumption

$$
R_i(\theta^c_{i,p}, \alpha^c_{i,b}, t) = 
\begin{cases} 
\theta^c_{i,p}(t) \cdot \beta^c_{p,b} \cdot \alpha^c_{i,b}(t) \log \left( 1 + \gamma^b_{i,p}(t) \right), & \text{if } \alpha^c_{i,b}(t) = 1, \\
0, & \text{otherwise}.
\end{cases}
\tag{1}
$$
 
- $$ \alpha^c_{i,b} $$: Binary indicator 변수 (user $$i$$가 SBS $$b$$, slice $$c$$에 의해 서비스 유무 판단)
- $$ \beta^s_{p,b} $$: Slice $$c$$를 위한 PRB $$p$$의 bandwidth
- $$ \theta^c_{i,p} $$: QoS를 유지하기 위해 slice $$c$$로 할당된 PRB의 수
- $$ \theta_b^{max} $$: SBS $$b$$의 slice들에 할당될 수 있는 PRB의 합
  - $$
\hat{\theta}_b(t) = \theta_{\text{max}, b} - \sum_{c=1}^{C} \sum_{p=1}^{P} \sum_{i=1}^{I} \alpha^c_{i,b}(t) \cdot \theta^c_{i,p}(t)
$$ : SBS b안에 있는 잔여 PRB 수
- $$ \gamma^b_{i,p}(t) = {p_{i,b}(t)h_{i,p}^b(t)\over{\sigma^2}} $$: SNR
  - Transmission Power: $$ p_{i,b}(t) $$
  - Channel Gain: $$ h^b_{i,p}(t) $$
  - Noise: $$ \sigma $$

##### Channel gain

- IIoT 디바이스 $$i$$와 SBS $$b$$가 PRB $$p$$를 사용할 때의 channel gain
  

$$
h^b_{i,p}(t) = \displaystyle 10^{\frac{-PL_{i}^{\text{[dB]}}}{10}}
$$

##### NLoS path loss
- IIoT 디바이스의 path loss를 dB로 계산

$$
PL_{i,b}[\text{dB}] = A \log_{10}(d_{i,b}) + B + E \log_{10}\left(\frac{f_c}{5}\right) + X
$$
  - $$d_{i,b}$$: IIoT 디바이스와 SBS 사이의 거리
  - $$f_c$$: Carreir 주파수
  - $$X$$: 벽 투과 손실계수
  - $$A, B, E$$: propagation 모델에 따라 변함

##### 디바이스의 전송 에너지 소모

$$
\mathcal{E}_i(t) = \sum_{b=1}^{B} \sum_{c=1}^{C} \sum_{p=1}^{P} \frac{p_{i,b}(t) M^c_{i,b}}{R_i(\theta^c_{i,p}, \alpha^c_{i,b}, t)} \tag{4}
$$
  - $$M_{i,b}^{c}$$: 데이터 길이
    - 본 논문에서는 다수의 IIoT 기기가 같은 PRB $$P$$에 할당되지 않는다 가정함

#### 2. Backhaul Transmission and Queueing Delay

MEC-BS: M/M/k queue로 모델링
- $$k$$개의 virtual computing resource blocks (vCRB)
- 한개의 queue $$Q_{mec}$$ 
- SBS로부터 오는 Arrival, Service 프로세스의 traffic은 Poisson 분포를 따른다고 가정
  - Arrival rate: $$\lambda_{mec}$$
    - $$\lambda_{mec} = \lambda_{b} + \sum_{b' \in\mathcal{B}} \lambda_{b'}$$ ($$b' \in \mathcal{B} \backslash {b}$$)
  - Service rate: $$\mu_{mec}$$
- MEC utilization: $$\rho_{\text{mec}} = \frac{\lambda_{\text{mec}}}{k \times \mu_{\text{mec}}} \tag{5} $$ 



**$$
M_b = \sum_{t=0}^{T} \sum_{c \in C} \sum_{p \in P} \sum_{i \in I} R_i(\theta^c_{i,p}, \alpha^c_{i,b}, t)
$$
의 데이터를 보내는데 걸리는 total delay(Transmission delay + queuing delay)** : $$
d_{b,\text{mec}} = 
\begin{cases} 
\frac{M_b}{R^{\text{mec}}_b(t)} + \frac{C(k,\rho_{\text{mec}})}{k \mu_{\text{mec}} - \lambda_{\text{mec}}}, & \text{if } \sum_{c \in C} \sum_{i \in I} \alpha^c_{i,b}(t) \geq 1, \\
0, & \text{otherwise}.
\end{cases}
\tag{6}
$$

- $$ \mathcal{C}(k, \rho_{mec}) $$: Erlang's C formula(큐잉 이론에서 사용되며 시스템의 delay 확률을 계산하는데 유용)
- SBS에서 vCRB 서비스 요청이 증가함에 따라 일부 SBS 서비스 요청은 MEC-BS에서 free vCRB에 접근하지 못할 수 있다. 이 때, MEC-BS는 SBS의 vCRB의 서비스 요청을 거절하지 않고, free vCRB가 생길 때까지 이러한 요청을 $$Q_{mec}$$에 추가한다.

MEC-BS와 SBS $$b$$ 사이 wireless backhaul capacity

$$
R_b^{\text{mec}}(t) = \frac{\beta_{\text{mec}}}{|B|} \log \left( 1 + \gamma_{b,\text{mec}}(t) \right). \tag{7}
$$

- $$\gamma_{b, mec}(t)$$: SBS b와 MEC-BS간의 SNR을 나타냄.

#### 3. AOI

$$
\delta_i(t + 1) = (1 - \alpha^c_{i,b}(t)) \left[ \frac{1}{\xi_i} \cdot f(\delta_i(t)) \right] + \alpha^c_{i,b}(t) \left[ \frac{M^c_{i,b}}{R_i(\theta^c_{i,p}, \alpha^c_{i,b}, t)} + d_{b,\text{mec}} \right]. \tag{8}
$$

- $$f(\cdot)$$: non-negative하고 non-decreasing인 age penalty funciton
  - $$\xi_{i}$$: IIoT $$i$$의 priority
  - 일반적으로 age penalty function은 exponential 함수이다.
- 식의 첫번째 부분은 높은 priority의 IIoT가 자주 스케쥴 되지 않을 때 penalize
  - 스케쥴링 되지 않으면 AoI가 penalty function으로 결정되고, 스케쥴링 되면 실제 transmission latency가 AoI에 반영됨

### 2.3.C Problem Formulation

위의 model들을 통해 IIoT 디바이스들과 AoI 함수들은 $$\theta$$, $$\alpha$$로 제어된다. 

문제의 objective는 각 IIoT 디바이스들의 AoI가 미리 정해진 threshold $$\xi_{i}$$를 넘을 확률을 최소화하는 것이다. 또한 이 objective들은 elastic network slicing을 위해 QoS 제약조건들을 고려하여 매핑된다. 
{:.note}
---

따라서 optimization 문제는 다음과 같이 정의된다.

$$
\min_{\alpha, \theta} \sum_{i=1}^{I} \Pr\{\delta_i > \xi_i\}, \tag{9}
$$

subject to

$$
\frac{1}{T \cdot I} \sum_{t=1}^{T} \sum_{i=1}^{I} \mathcal{E}_i(t) \leq \mathcal{E}', \quad \forall i \in \mathcal{I}, \tag{10}
$$

**(10)**: IIoT 디바이스들이 maximum energy threshold $$\mathcal{E}'$$를 넘지 않게 하는 제약 조건
{:.note}

$$
0 \leq \sum_{i \in \mathcal{I}} \alpha^c_{i,b}(t) \leq \alpha^{\text{max}}_{b,c}, \quad \forall b \in \mathcal{B}, \forall c \in \mathcal{C}, t = 0, \ldots, T, \tag{11}
$$
  

$$
\alpha^c_{i,b}(t) \in \{0, 1\}, \quad \forall i \in \mathcal{I}, \forall b \in \mathcal{B}, \forall c \in \mathcal{C}, t = 0, \ldots, T, \tag{12}
$$

**(11),(12)**: 각 슬라이스에 의해 서비스되는 IIoT 디바이스 연결 수 제한
{:.note}

$$
\sum_{p \in \mathcal{P}} \theta^c_{i,p}(t) \geq \theta^{\text{min}}_i, \quad \forall c \in \mathcal{C}, \forall i \in \mathcal{I}, t = 0, \ldots, T, \tag{13}
$$

**(13)**: 슬라이스에 할당되어 있는 PRB의 수가 IIoT 디바이스들의 최소 조건인 $$\theta^{min}$$을 만족해야 함
{:.note}

$$
\sum_{p \in \mathcal{P}} \theta^c_{i,p}(t) \leq 1, \quad \forall c \in \mathcal{C}, t = 0, \ldots, T, \tag{14}
$$

**(14)**: 각 PRB $$p \in mathcal{P}$$는 여러개의 슬라이스들에 할당될 수 없음
{:.note}

$$
\sum_{c \in \mathcal{C}} \sum_{p \in \mathcal{P}} \sum_{i \in \mathcal{I}} \alpha^c_{i,b}(t) \cdot \theta^c_{i,p}(t) \leq \theta^{\text{max}}_b, \quad \forall b \in \mathcal{B}, t = 0, \ldots, T. \tag{15}
$$

**(15)**: IIoT 디바이스들에게 할당되는 PRB의 수는 SBS의 최대 PRB 수를 넘어서면 안됨
{:.note}

- (9): Stochastic optimization problem
- (11), (12): Integer(non-convex) constraints

Practical한 환경에서
- Fixed value 문제: O-RAN 시스템에서 $$\theta_{max, b}$$와 $$\alpha_{max, b, c}$$라는 고정된 값 때문에, 채널 상태와 PRB 수요가 예측하기 어려운 패턴을 보임
- 다양한 슬라이스: 문제 (9)를 해결하기 위해서 다양한 IIoT 슬라이스 유형이 서로 PRB를 두고 경쟁하는 상황을 고려해야 함
- 제약 조건 유지: 이 경쟁 속에서도 O-RAN 슬라이스의 격리와 관리 제약(13-15)를 준수해야 함

## 2.4 Matching Game and Deep Reinforcement Learning for Elastic O-RAN Slicing

![Figure2](/assets/img/blog/elastic-o-ran/Figure2.png)

### 2.4.A O-RAN Control Layer

`near-RT` 안에 있는 *radio connection management*를 사용하여 radio connectivity 관리를 실시
  - Matching game을 이용하여 stable한 IIoT association을 찾음
  - IIoT 디바이스와 SBS간의 안정적인 무선 연결을 생성하여 O-RAN 최적화 프로세스가 각 네트워크 entity에 의해 two-sided 분산 의사 결정을 가능케 하기 위함
    - 분산 의사 결정을 통해 각각의 장치와 기지국이 중앙에서 통제받지 않고 독립적으로 결정일 내릴 수 있음

###  2.4.B O-RAN Management Layer
  - DDPG = Q-learning + Policy gradients 를 호스팅하여 QoS constraints를 가지고 있는 자원 할당 문제를 해결 
  - *design, policy, and configuration* 모듈들이 학습 모델을 디자인하고 선택, 학습된 module을 non-RT RIC에서 Control layer에 있는 near-RT RIC으로 보내줌
  - Two-sided matching game과 DDPG를 합친 것의 장점
    - Matching game이 자원 할당에 대한 long-term policy-based guidance를 강화하는데 도움을 줌
    - Matching game을 통해 안정적이고 보장된 무선 연결을 활용할 수 있음
    - 모든 IIoT 디바이스와 SBS의 만족도를 반영하여 트렌드와 패턴을 반영할 수 있음
    - 학습 module이 matching game의 출력을 관찰하여 모델을 훈련하면, IIoT 환경의 dynamic 변화에서도 무선 연결 관리를 빈번하게 실행할 필요가 없음
    - 계산 복잡도 감소

### 2.4.C Radio Connection Management via Distributed Matching
상응하는 SBS $$\alpha_{i,b}^c$$와 IIoT의 association을 얻기 위해 association 문제를 one-to-many Hospitals/Residents (HR) problem으로 정의하였다.

Residents: IIoT 디바이스 $$\mathcal{I}$$
Hospitals: SBSs $$\mathcal{B}$$

각각의 IIoT 디바이스들은 선호하는 기지국 리스트를 가지고 있다. 이 때, IIoT 디바이스는 SNR에 기반하여 내림차순으로 리스트를 나열한다.

- $$\mathcal{A}$$: Acceptable pair

- $$\alpha_{i,b}^{c} \in \tilde{\mathcal{A}} \subset \mathcal{A}$$: 상호 acceptable한 IIoT 디바이스 $$i$$와 $$b$$

*Definition 1 (Distributed matching game)*: matching $$\tilde{\mathcal{A}}$$는 IIoT 디바이스 $$i$$와 SBS $$b$$ 사이의 assignment

- $$\left\vert \tilde{\mathcal{A}}(i) \right\vert \le 1$$, $$\forall i \in \mathcal{I}$$


IIoT 디바이스 i는 최대 하나의 SBS에 할당될 수 있다.
{:.note}

- $$\left\vert \tilde{\mathcal{A}}(b) \right\vert \le \alpha_{b,c}^{max}$$, $$\forall b \in \mathcal{B}$$


SBS는 overloading을 줄이기 위해 특정 수의 IIoT 디바이스만 할당할 수 있다.
{:.note}


- 알고리즘 다이어그램
  
![Algorithm1](/assets/img/blog/elastic-o-ran/Algorithm1.png)

최종적으로 blocking pair가 없을 때, matching outcome $$\alpha_{i,b}^{c} \in \tilde{\mathcal{A}} \subset \mathcal{A}$$이 만들어진다.

### 2.4.D Actor-Critic Model with DDPG for RIC non-RT

본 논문에서 제안하는 DDPG는 4개의 neural network들로 구성되어 있으며, O-RAN management layer에 배치된다.

더 정확하게 설명하자면,

- *actor* part: target Q-network
  - Policy 네트워크를 유지
  - **Elastic slicing**을 위한 상태 정보를 입력으로 받음
  - Actor 모델은 이 입력을 바탕으로 continuous 액션을 생성함


- *critic* part: Q-network
  - Q-value 네트워크를 유지
  - Actor 네트워크와 같은 입력을 받음
  - Critic 모델은 이 입력을 바탕으로 Q-value를 출력
  

O-RAN 환경에서 명시적인 state 및 action space의 정의가 필요하다. `Distributed matching game`을 통한 무선 연결 관리와 측정값이 state 공간에 포함되며, DDPG 기반의 elastic O-RAN slicing 동작과 성능을 향상시키기 위해 사용된다.

#### 2.4.D.1 State and Action Spaces

- State: $$ s_{t} \in \mathcal{S}, s_{t} = \{\hat{\theta}(t - 1), \delta(t - 1), \mathcal{E}(t - 1) \} $$
  - 잔여 capacities: $$ \hat{\theta} $$
  - AoI metric 값: $$ \delta $$
  - 에너지 소모량: $$ \mathcal{E} $$

- Action: $$ a_{t} = \{a_{b,t}\}_{b \in \mathcal{B}} $$

각 SBS $$ b $$의 PRB(Physical Resource Block) 할당 수는 IIoT 장치의 슬라이스 요구 사항에 따라 다양한 슬라이스 유형에 분배된다. 
{:.note}

#### 2.4.D.2 Reward Function

$$
r_t = 
\begin{cases} 
\sum_{i}^{I} \omega \cdot (1 - \Pr\{\delta_i > \xi_i\}), & \text{if constraints (10)-(15) are true}, \\
-\sum_{i}^{I} (1 - \omega) \cdot \Pr\{\delta_i > \xi_i\}, & \text{otherwise}.
\end{cases}
$$

$$ \omega $$: 0.1과 1 사이의 값으로 설정. 1에 가까울수록 constraint violation 시 패널티를 감소시킴.

#### 2.4.D.3 Deep Deterministic Policy Gradient With Actor-Critic Model

![Algorithm2](/assets/img/blog/elastic-o-ran/Algorithm2.png)

1. Actor & Critic 네트워크와 target 네트워크들이 상응하는 네트워크 weight를 랜덤으로 초기화

2. DDPG 알고리즘이 replay buffer $$\mathcal{M}$$를 사용하여 experience를 샘플링하고 네트워크 파라미터 업데이트

3. Association list $$ \alpha_{i, b}^{c} \in \tilde{\mathcal{A}} \subset \mathcal{A}$$ 이 네트워크 자원들을 할당하기 위해 생성됨

4. 각 SBS에서 Adaptive 네트워크 슬라이싱이 연관된 IIoT 디바이스들의 요구에 맞춰 진행됨
- Actor-critic 네트워크를 사용하여 학습하는 에이전트는 정책 $$\\mu(s \mid \theta)$$을 활용하여 액션 $$a_t$$을 취함
- Slice action vector에 따라 action $$a_t$$를 실시

5. Action $$a_t$$에 기반하여 IIoT association, 에너지 소모, AoI, 순간 reward가 계산이 된 후 다음 상태 $$s_{t+1}$$로 넘어간다.
- 이 때, 모든 state들의 transition들은 메모리 buffer에 저장이 되고 memory $$\mathcal{M}$$으로부터 샘플링된 후, actor&critic 네트워크를 업데이트하기 위해 쓰임

6. Value 네트워크는 아래와 같은 Bellman 방정식을 이용해 업데이트 됨
\begin{equation}
y_m = r_m + \gamma \left[ Q'\left( s_{t+1}, \mu'\left( s_{t+1} \middle| \theta^- \right) \middle| \psi^- \right) \right]. \tag{19}
\end{equation}

7. 다음 상태의 Q-value들이 target value network와 target policy network와 함께 계산됨
- $$\theta^{-}$$: Actor's target 네트워크의 weight
- $$\psi^{-}$$: Critic's target 네트워크의 weight

8. 업데이트 된 Q-value와 Original Q-value 값의 mean-squared loss를 최소화시킨다. 
\begin{equation}
\mathcal{L} = \frac{1}{|\mathcal{M}|} \sum_{m=1}^{|\mathcal{M}|} (y_m - Q(s_m, a_m | \psi))^2. \tag{20}
\end{equation}
- $$\psi$$: Critic 네트워크의 weight

9. Policy 함수에 대해 목적함수는 보상의 기댓값을 최대화시키는 것이고, 아래와 같이 계산된다.
\begin{equation}
J(\theta) = \mathbb{E} \left[ Q(s, a) \mid_{s = s_t, a = \mu(s_t)} \right].
\end{equation}
- Policy loss 계산
  -  
  $$
  \nabla_{\theta_\mu} J(\theta) \approx \nabla_a Q(s, a) \nabla_{\theta_\mu} \mu(s | \theta^\mu).
  $$
  -  
  $$
  \nabla_{\theta} J \approx \frac{1}{|\mathcal{M}|} \left[ \sum_{m=1}^{|\mathcal{M}|} \nabla_a Q(s, a | \psi) \bigg|_{s=s_m, a=\mu(s_m)} \nabla_\theta \mu(s | \theta) \bigg|_{s=s_m} \right].
  $$

### 2.4.E Complexity Analysis

Algorithm 1의 complexity는 SBS와 IIoT의 분산된 preference list를 만들어내는 것으로부터 측정된다.
- 표준 정렬 알고리즘을 이용하면, $$ \mathcal{O} (\left\vert \mathcal{B} \right\vert log(\left\vert \mathcal{B} \right\vert)) $$ 그리고 $$ \mathcal{O} (\left\vert \mathcal{I} \right\vert log(\left\vert \mathcal{I} \right\vert)) $$가 된다.
- IIoT 디바이스마다 preference list의 제한이 있기 때문에 에너지 소모면에서 감당이 가능할 것으로 예상한다.

Algorithm 2는 4개의 neural network들을 buffer 메모리 안의 정보들을 이용하여 training하고 step $$t$$마다 action $$a_t$$를 계산해야 하기 때문에 상당한 복잡도를 가질 것으로 예상한다. 
- Training의 시간복잡도는 $$\mathcal{O}(s)$$, 공간복잡도는 $$\left\vert \mathcal{M} \right\vert $$이 된다.
- 본 논문은 기존 선행 연구들과 다르게 O-RAN 아키텍쳐와 인터페이스를 활용하여 **머신 러닝**과 **게임 이론 기반 모델 훈련**을 용이하게 함으로써 long-term policy-based guidance를 강화하고, 모든 IIoT 장치 및 SBS의 슬라이싱 정책에 대한 만족도의 경향과 패턴을 반영한다.

## 2.5 Experimental Results and Analysis

![Table2](/assets/img/blog/elastic-o-ran/Table2.png)

Elastic 슬라이싱 알고리즘을 만들기 위해, RL agent를 훈련시켜 hyper-parameter를 맞춘다. Actor와 Critic 네트워크의 neural 네트워크 구조는 [512, 512] hidden layer, `ReLU` activation function을 사용한다.

본 논문은 baseline 아키텍쳐로 `Tensorflow`, `Ray RLlib:Scalable Reinforcement Learning`을 사용하였다.

### 2.5.A Experiment Setting

- 30%(emergency systems, Type 1), 30%(scale reading services, Type 2), 40%(mobile robots, Type 3)
- Channel Model: alpha-beta-gamma (ABG) model [5G channel model for the path loss model of the IIoT scenarios]

본 논문에서 사용한 알고리즘인 DDPG with matching approach는 아래의 두 알고리즘들과 비교된다.
- Proximal Policy Optimization(PPO)
- Policy Gradient{PG} 

### 2.5.B Learning Efficiency Analysis

Learning efficiency를 분석하기 위해 제안 알고리즘과 다른 baseline 알고리즘들을 normalized 에너지 소모, normalized AoI penalty function, 그리고 훈련 시 한 에피소드 당 reward로 비교하였다.

![Figure3](/assets/img/blog/elastic-o-ran/Figure3.png)

그래프를 보면 알 수 있듯이 제안한 DDPG 알고리즘이 다른 baseline PPO, PG 알고리즘보다 에너지 소모가 더 많은 것을 알 수 있다. 이는 제안한 DDPG 알고리즘이 슬라이스에 더 많은 IIoT 디바이스들을 연결시킬 수 있기 때문이다. 하지만 제안 알고리즘은 energy consumption threshold보다 낮은 normalized average energy consumption을 만족시킨다.


![Figure4](/assets/img/blog/elastic-o-ran/Figure4.png)

그래프를 보게 되면 PPO, PG 알고리즘의 normalized AoI penalty가 제안 알고리즘보다 더 높은 것을 확인할 수 있다.

![Figure5](/assets/img/blog/elastic-o-ran/Figure5.png)

세 가지 접근법 모두 일부 변동에도 불구하고 서로 다른 timestamp에서 final level에 수렴하는 것을 확인할 수 있다. 이는 네트워크 내의 다양한 슬라이스 유형에 대한 dynamic demand와 policy exploration 때문이다. 제안 DDPG 알고리즘은 episode 100에서 positive reward를 반환하여 더 높은 수렴 속도를 달성하는 반면, baseline 접근법들은 더 높은 penalty를 받는다.

### 2.5.C Key Performance Analysis

![Figure6](/assets/img/blog/elastic-o-ran/Figure6.png)

Fig. 6를 보게되면 제안된 DDPG 알고리즘이 다른 baseline 접근법들을 확실히 능가하는 것을 확인할 수 있다. 제안 DDPG 알고리즘은 PPO와 PG보다 더 다양한 IIoT 디바이스들을 연결시킬 수 있다.

하지만 고정된 수의 SBS와 네트워크 내 최대 PRB(Physical Resource Blocks)에서 $$\left\vert \mathcal{I} \right\vert = 50 $$ 이후부터 다양한 IIoT 장치 유형을 서비스하는 비율이 감소하기 시작한다. 이는 IIoT 장치 수가 증가함에 따라 채널 조건에 의존하는 안정적인 무선 연결 관리를 보장하기 위해 matching game에서 IIoT 장치 연결수가 감소하기 때문이다.

baseline 접근법들은 서로 다른 IIoT 서비스 타입 간 불균형한 service rate를 보이는데 이는 DDPG보다 sample observation의 효율이 떨어지기 때문이다.

![Figure7](/assets/img/blog/elastic-o-ran/Figure7.png)

제안 DDPG의 전반적인 에너지 소모는 다른 baseline 알고리즘들보다 더 높다. 이는 앞에서 언급한 것처럼 IIoT 디바이스의 연결 수가 많아졌기 때문이다.

![Figure8](/assets/img/blog/elastic-o-ran/Figure8.png)

마찬가지로 제안 DDPG 알고리즘이 normalized AoI에서 다른 접근법들을 압도하는 성능을 보여준다. IIoT 디바이스 수가 낮은 네트워크에서 SBS들은 대부분의 IIoT 디바이스들을 동시에 지원할 수 있다. 따라서, IIoT 디바이스들은 AoI threshold를 위반하지 않고 빈번하게 SBS들과 연결될 것이다. 하지만 네트워크 크기가 증가함에 따라 SBS들은 더 적은 IIoT slice type 디바이스들에게 PRB들을 할당해주게 된다. 따라서 DDPG의 AoI penalty는 증가하게 되는데, 이렇게 해도 다른 알고리즘들보다는 낮은 값을 가지게 된다.

![Figure9](/assets/img/blog/elastic-o-ran/Figure9.png)

누적 보상합에 대해선 DDPG 접근법이 다른 baseline 접근법들을 능가하게 된다. 네트워크 사이즈가 $$\left\vert \mathcal{I} \right\vert = [50, 80] $$일 때, 누적 보상합은 안정화된 경향을 보인다. 

하지만 네트워크 사이즈가 거대할 때(i.e., $$\left\vert \mathcal{I} \right\vert = 100 $$), DDPG 알고리즘의 성능은 소폭 감소하게 되는데 이는 수요의 증가와 PRB의 공급의 불일치로 인해 발생한다.

네트워크 사이즈가 바뀌더라도 제안 DDPG 알고리즘이 더 높은 누적 보상합을 가지는 것은 , 제안 DDPG 알고리즘이 다른 두 baseline들보다 개선된 `AoI violation vs. energy efficiency of the IIoT 디바이스`을 가지는 것을 의미한다.

![Figure10](/assets/img/blog/elastic-o-ran/Figure10.png)

위의 그래프에선 알고리즘들의 실행 시간을 비교하였다. 제안 DDPG 알고리즘이 다른 알고리즘들보다 빠른 학습 시간을 보였는데, 이는 제안 DDPG 알고리즘이 IIoT 서비스 타입들의 historical demand를 이용하여 future decision들을 만드는 반면, 다른 알고리즘들은 buffer를 사용하는 데 비효율적이기 때문이다.

![Figure11](/assets/img/blog/elastic-o-ran/Figure11.png)

SBS 수의 증가가 latency threshold에 미치는 영향을 평가하였다. Latency threshold는 각각 50ms(Type 1), 100ms(Type 2), 500ms(Type 3)이다. SBS의 수가 늘어날수록 각 SBS들의 혼잡도가 줄어들고, 슬라이스 당 지연이 감소하며 더 많은 IIoT 장치들이 latency threshold 내에 머무르게 된다.

- Empirical Cumulative Distribution Function (ECDF): 경험적 누적 분포 함수

ECDF가 SBS의 수가 증가함에 따라 Type 1 슬라이스 유형 IIoT 장치의 66%, 93%, 100%가 latency threshold 내에 머무르게 된다. 대체적으로 SBS의 수가 적을 때 latency violation이 더 크다. 이는 orthogonal PRB들이 다른 IIoT 슬라이스에게 균등하게 할당되기 때문이다.

하지만 SBS의 수가 일정 수준을 넘어서면, 추가적인 SBS의 도입이 더 이상 큰 성능 향상을 가져오지 않는 law of diminishing marginal returns가 적용된다. 쉽게 말하자면, Type 2와 Type 3 서비스에서 SBS가 추가되더라도 latency 개선이 크게 나타나지 않는 것을 의미한다.

### 2.5.D Discussion

$$
\delta_i(t + 1) = (1 - \alpha_i^{c,e}(t)) \left[ \frac{1}{\xi_i} \cdot f(\delta_i(t)) \right] + \alpha_i^{c,e}(t) \left[ \frac{M_i^{c,e}}{R_i(\theta_i^{c,p}, t)} + d_{b,\text{mec}} \right]
$$
- 위의 Objective 함수를 통해 delay, throughput, AoI performance metric의 strong connection을 포착할 수 있다.
  
- MEC-BS queue buffer가 overflown되면 일부 SBS service request들은 여유 MEC-BS 서버에 접근할 수 없고 이는 데이터의 손실을 유발한다.
- 제안 DDPG 알고리즘은 PG와 PPO와 다르게 관측된 r델eward와 return보다는 학습단 value 함수에 기반하여 policy gradient를 계산하는 것이 더 유리하다.
- Actor-Critic이 있는 DDPG는 deterministic policy $$\phi(s)$$를 고려하기 때문에 성능 향상이 다른 알고리즘에 비해 높고 sample-efficient하다.


## 2.6 Conclusion

본 논문에서는 IIoT association 문제를 풀기 위한 matching game을 소개하였고, O-RAN slicing-based resource allocation을 위한 actor-critic-based deep reinforcement learning 모델을 사용하였다. 시뮬레이션에서 IIoT service rate와 energy consumption, 그리고 AoI 사이의 강력한 연관이 있음을 알 수 있다. 또한 네트워크 사이즈를 변경해가며 제안된 접근법을 추가로 검증하였다. 

# 3. Take Away


- Matching game 모델: one-to-many Hospital/Residents(HR) problem
- RAN-Slicing 모델: DDPG with actor-critic
- IIoT 시뮬레이션 환경에 대한 설명
  - IIoT 전용 채널 모델: ABG 모델 사용