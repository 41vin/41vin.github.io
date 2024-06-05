---
layout: post
title: A Lightweight Cell Switching and Traffic Offloading Scheme for Energy Optimization in Ultra-Dense Heterogeneous Netweorks
tags: [es]
image: /assets/img/blog/lightweight-cs/es.png
description: >
  **Authors**: Attai Ibrahim Abubakar, Hichael S. Mollel, Metin Ozturk, Sajjad Hussain, and Muhammad Ali Imran
sitemap: false
hide_last_modified: true
---

Cell-switching, HetNet, Traffic-offloading
{:.note title="Keywords"}


* this unordered seed list 􏰁ill be replaced by the toc
{:toc}




# 1. Why this paper

- Cell switching에 쓰이는 가벼운 알고리즘 구상에 필요
- Milan 데이터셋을 이용한 논문

# 2. Summary of paper

## 2.1 Introduction

UDN 환경에서 아래와 같은 다양한 방법들이 전력 소모 최적화를 위해 사용되어 왔다. 

- Antenna muting
- Cell zooming
- Power control
- Sectorization
- Dynamic cell switching operations

이 중 가장 전력을 절감할 수 있고 보편적으로 연구되고 있는 분야는 dynamic cell switching이다. 또한, cell switching은 기존 아키텍쳐에 최소한의 변화를 통해 실현시킬 수 있기 때문에 구현이 쉽다는 장점이 있다.

Cell switching을 위해 다양한 머신러닝 기법들이 제안되고 있다.

1. 지도학습(e.g., artificial neural networks (ANN))
2. 비지도학습(e.g., k-means)
3. 강화학습(e.g., deep and double-deep Q-learning)

**머신러닝 기반 알고리즘**은 뛰어난 generalization 능력을 가지고 있고 급변하는 네트워크 환경에 적응할 수 있지만, training process가 컴퓨팅 자원을 많이 소모하여 실시간 네트워크에 사용하기 힘든 단점을 가지고 있다.

**휴리스틱 기반 알고리즘**은 구현하기 쉽지만 generalization 능력이 부족하고, 급변하는 네트워크 환경에 적응할 수 없다는 단점을 가지고 있다.

본 논문에서는 optimal cell switching 기법으로 `exhaustive search algorithm (ES)`를 제안한다. 완전탐색은 사용 가능한 모든 SBS 조합을 탐색하기 때문에 최적의 조합을 찾아낼 수 있다. 하지만 이 방법은 SBS의 수가 늘어나면 컴퓨팅 자원 소모가 급격하게 늘어난다는 치명적인 단점을 가지고 있다.

`ES`를 보완하기 위해 대안 방법으로 생각해낸 것이 clustering을 이용해 탐색 공간을 줄이고 완전탐색을 하여 소모 시간을 줄이는 것이다. 본 논문에서는 이를 기반으로 만든 `Threshold-based Hybrid cEll swItching Scheme(THESIS)` 알고리즘을 제안한다.

## 2.2 Contributions

1. **THESIS**, k-means + ES 알고리즘을 통해 UDHN에서의 에너지 최적화를 실현시킴. 제안 알고리즘은 computationally 효율적이고 optimal 솔루션에 가까운 결과를 냄. 또한, 대규모 네트워크 환경에도 적용 가능함.
2. 비교 알고리즘은 단순히 k-means만 사용
3. $$CO_{2}$$ 절감량도 측정
4. 제안 알고리즘과 비교 알고리즘의 엄밀한 비교를 통한 computational 효율성 검증
5. 실제 네트워크 데이터를 이용하여 알고리즘의 scalability와 성능을 검증


## 2.3 System Model

### 2.3.A Network model

여러 개의 macro cells(MCs)로 이루어진 UDHN(Ultra Dense Heterogeneous Netweork) 환경을 고려한다. 각각의 MBS는 각기 다른 capacity와 power consumption을 가지고 있는 SBS들을 포함하고 있다.

UDHN은 CDSA(Control and Data Separated Architecture)를 채택한다.

- MBS: 제어 BS를 담당하며 SBS들을 낮은 data rate로 커버리지 내에서 서비스
- SBS: 데이터 BS를 담당하며 높은 데이터 트래픽이 필요한 서비스 담당

**Vertical traffic offloading**: SBS가 traffic load가 없거나 적을 때, MBS로 traffic을 offloading

본 논문에서 정의하는 `QoS`: cell switching이 일어난 후 서비스 가능한 총 트래픽 양

![Figure.1](/assets/img/blog/lightweight-cs/figure.1.png)

### 2.3.B UDHN의 소모 전력

BS의 power 소비는 다음과 같다.

$$P_{BS}(\lambda, t) = P_{o} + \lambda_{t}\eta P_{tx} $$

- $$P_{o}$$: constant circuit power 소모
- $$ \lambda_{t} $$: 순간적인 traffic 로드
- $$ \eta $$: 로드와 관련있는 power 소모 컴포넌트
- $$ P_{tx}$$: BS의 송신 전력

이 때 $$P_{o}, \eta, P_{tx}$$는 **SBS의 종류**에 따라 다르다.

UDHN의 총 소모 전력 $$P_u$$는 다음과 같다. 

$$P_{u}(\lambda, t) = \sum_{i} \sum_{j} P_{BS_{i,j}}(\lambda, t) $$

- $$P_{BS_{i,j}}$$: $$i$$번째 MC(Macro Cell)에 속해있는 $$j$$번째 BS의 소모 전력
- $$P_{BS_{i,1}}$$: $$i$$번째 MC에 속해있는 MBS의 소모 전력

## 2.4 Problem Formulation
$$T$$는 $$L$$의 길이를 가지는 duration으로 나뉜다. 이 때 $$u=[1,2,...,K]$$와 같이 $$T$$ 안에 있는 $$L$$ 길이의 블록 순서를 나타낼 수 있다. $$K$$는 $$T$$안에 있는 블록의 수이다($$K=T/L$$).

UDHN이 SBS가 낮은 트래픽 load를 가질 때 전원을 끄는 시나리오를 고려한다.
{:.note}

본 논문의 목적은 각 time slot에서 최소한의 전력 소모를 실현시킬 수 있는 최적의 switching strategy를 결정하는 것이다.

$$ P_u(\lambda, \Gamma_{i,j}) = \sum_{u} \sum_{i} \sum_{j} [\Gamma_{i,j}P_{BS_{i,j}}(\lambda,t) + (1-\Gamma_{i,j})P^{s}_{BS_{i,j}}] $$

- $$P^{s}_{BS_{i,j}}$$: BS가 sleep mode일 때 소모 전력
- $$\Gamma_{i,j}$$: t일 때, $$(i,j)^{th}$$ BS의 off/on 상태 (On일 때 1, Off일 때 0)

따라서 power optimization objective function은 다음과 같이 작성할 수 있다.

$$ \begin{align*}
\max_{\Gamma_{i,j}}\quad & P_u(\lambda, \Gamma_{i,j}), \tag{5} \\
\text{s.t}\quad & z = \lambda_{i,1} + \sum_{j=2}^m \lambda_{i,j} \Gamma_{i,j}, \tag{6} \\
& \hat{\lambda}_{i,1} \leq \lambda_{i,1}^m, \tag{7} \\
& \Gamma_{i,j} \in \{0, 1\}. \tag{8}
\end{align*} $$

- Constraint (6): UDHN에 의해 지원되는 traffic demand는 cell switching과 traffic offloading 이전/이후 동일
    - $$z:$$ Cell switching 이전 UDHN에 의해 지원되는 전체 traffic demand
- Constraint (7): SBS에 의해 offloading 되는 traffic 총량이 MBS의 maximum traffic demand를 넘을 수 없다.
    - $$\hat{\lambda}_{i,1}$$: Traffic offloading 이후 MBS의 traffic load
    - $$\lambda^{m}_{i,1}$$: MBS가 최대로 수용할 수 있는 traffic demand
- Constraint (8): SBS의 상태는 on/off 둘 중 하나여야 함

## 2.5 Proposed Cell Switching Scheme

최적 알고리즘으로 소개된 ES는 네트워크 규모가 커지면 계산 자원 요구량이 커지기 때문에 적합하지 않다. 이를 해결하기 위해 본 논문에서는 THESIS(k-means clustering + ES) 알고리즘을 제안한다.


### 2.5.A Cell clustering

**Selection of optimal number of clusters (elbow method)**

클러스터링에서 가장 중요한 것 중 하나는 데이터셋을 나눌 클러스터의 수를 결정하는 것이다.

`elbow method`를 사용하여 최적의 클러스터 수를 결정할 수 있다.  `elbow method`에서 최적의 클러스터 수는 SSE(Sum of the Squares Errors)의 합으로 결정된다.

$$ SSE = \sum^{N}_{k=1}\sum(X-c_{k})^2 $$

- $$k$$: 클러스터의 수
- $$X$$: 클러스터 안에 있는 data 포인트
- $$c_k$$: 특정 클러스터의 평균값(중앙값)

SSE 곡선이 평탄화되기 전, elbow 모양을 보일 때 k값을 사용한다
{:.note}

### 2.5.B Multi-level clustering based cell switching scheme

MLC(Multi-level clustering) 알고리즘은 MBS의 커버리지 내에서 clustering과 light traffic load 된 SBS들을 MBS로 offload 하는 과정을 반복하여 최적의 클러스터 구성을 찾아낸다.

![figure.2](/assets/img/blog/lightweight-cs/figure.2.png)

알고리즘의 단계를 정리하면 다음과 같다.

1. **Elbow 방법을 사용하여 최적의 클러스터 수 결정**: 데이터 포인트들을 클러스터링 하여 각 클러스터의 변동성이 최소가 되는 클러스터의 수를 찾는다.
2. **k-평균 알고리즘을 적용하여 SBS 클러스터링**: 최적의 클러스터 수를 기반으로 각 SBS의 트래픽 부하에 따라 첫 번째 레벨 클러스터링을 수행한다.
3. **클러스터별 총 트래픽 부하 계산 및 비교**: 각 클러스터의 트래픽 부하를 계산하고 이를 MBS에서 사용 가능한 무선 자원과 비교하여 off 할 수 있는 클러스터 수를 결정한다.
4. **선택된 클러스터의 트래픽을 MBS로 오프로딩 후 네트워크의 전력 소비 계산**: 선택된 클러스터의 트래픽을 MBS로 오프로딩하고, 그 결과로 네트워크의 전력 소비를 계산한다.
5. **클러스터 추가 분할**: 남은 클러스터 중 MBS가 처리할 수 있는 최대 트래픽 수요를 초과하는 클러스터는 더 작은 클러스터로 추가 분할하여 이전 단계들을 반복한다. 이 과정은 하나의 SBS만이 남을 때 까지 또는 더 이상 처리할 SBS가 없을 때까지 반복한다.
6. **최종적인 전력 소비 계산 및 순위 매기기**: 모든 클러스터링과 트래픽 오프로딩 작업 후 UDHN의 전력 소비를 계산하고, 얻은 전력 소비 값들을 오름차순으로 순위를 매겨 가장 낮은 전력 소비를 보이는 구성을 최적의 클러스터로 선택한다


### 2.5.C Threshold-based Hybrid Cell Switching Scheme

앞에서 설명한 MLC 알고리즘이 대규모 네트워크에 적용할 수 있더라도 ES 알고리즘에 비해 sub-optimal 한 결과를 내게 된다. 어떠한 sub-optimal 알고리즘도 결국 ES 알고리즘과 근사한 결과를 낼 수 밖에 없다는 점을 기억하자.

이를 보완하고자 본 논문에서는 THESIS 알고리즘을 제안한다.
![Figure.3](/assets/img/blog/lightweight-cs/figure.3.png)

MLC 방법과 같이 최적의 클러스터 수를 결정하는데 elbow 방법이 사용된다. 그 다음 k-means 알고리즘을 사용하여 SBS의 트래픽 로드를 기반으로 한 첫번째 단계의 클러스터링이 수행된다. 그 후, threshold value($$B_{th}$$)보다 작은 SBS 수를 가지는 클러스터가 결정된다. 그 클러스터는 ES 알고리즘을 이용해 전원을 꺼야 할 SBS를 결정하고 네트워크의 소모 전력이 계산된다.

$$B_{th}$$보다 큰 수의 SBS를 가지는 클러스터는 다시 한번 k-means 알고리즘을 사용해 재 클러스터링하고 ES를 적용하여 꺼야 할 SBS 수를 결정하고 네트워크의 전력 소비를 계산한다. 마지막으로, 다양한 SBS 세트를 꺼서 얻은 UDHN의 전력 소비를 기준으로 순위를 매긴다. 가장 작은 전력 소비를 보이는 SBS 세트를 최적의 꺼야 할 SBS 세트로 선택한다.

MLC와 다르게 THESIS 알고리즘은 클러스터 내에서 전원을 꺼야할 SBS를 고른다. 이를 통해 THESIS는 SBS들의 타입들(capacity and power consumption profile)을 구분할 수 있다.

## 2.6 Performance Evaluation
본 논문에서는 하나의 Macro cell 만을 가지고 시뮬레이션을 진행하였으며, 이 결과의 scalability가 증명되었기 때문에 상관없다고 언급한다. 시뮬레이션에서 사용한 파라미터는 아래의 그림과 같다.

![Figure.4](/assets/img/blog/lightweight-cs/figure.4.png)

### 2.6.A Traffic data and simulation parameters
UDHN의 전체 전력을 계산하기 위해서는 MBS와 SBS들의 traffic load가 필요하다.

Milan 데이터셋의 inter activity level을 BS의 traffic load로 고려하였다. 임의로 선택한 두 개의 그리드를 결합하여 MBS의 트래픽 부하로 나타냈고, SBS의 경우 단일 그리드로 고려하였다. 그 후, 트래픽 부하는 UDHN의 각 SBS의 무선 자원 양에 따라 정규화 되었다.

### 2.6.B Performance metrics
- **Power Consumpiton**: UDHN 시뮬레이션 도중 소비한 instantaneous 소모 전력
- **Energy Saved**: 24시간 동안 절감된 에너지 양을 측정
- **Carbon Emission**: $$\mathcal{E}_{CO_2} = \zeta\sum_{t=1}^{T}E_{u,t}$$ ($$\zeta$$: $$CO_2$$ conversion factor)
- **Average Network Throughput**: Active BS들에 의해 서비스될 수 있는 총 네트워크 demand, $$\mathcal{T}_{N}(t) = \mathcal{T}_{i,1}(t) + \sum_{j=2} \mathcal{T}_{i,j}(t)$$

### 2.6.C Benchmarks

- **ES**: 항상 최적의 switching 패턴을 보장. 다른 cell switching 알고리즘의 목적은 이 알고리즘의 근삿값을 달성하는 것이다.
- **MLC**: ES에 비해 computation overhead가 적다. QoS를 보장하며 네트워크 규모가 클 때도 사용할 수 있지만 ES 방식에 비해 sub-optimal한 성능을 보인다.
- **AAO**: SBS들을 항상 켜 놓는 것으로, 네트워크 QoS를 보장하지만 에너지 절약 보장을 하지 못한다.

Q-learning과 같은 강화학습 기반 알고리즘들이 decision making으로 유명하지만 실제 상황에서는 모델 학습이 어렵고 무겁기 때문에 벤치마크 알고리즘에서 제외하였다.

### 2.6.D Results and Discussions

![Figure.5](/assets/img/blog/lightweight-cs/figure.5.png)

Traffic load의 양이 적을 때 small cell switching의 횟수가 많아지므로 traffic demand가 많을 때 소비 전력이 높고 traffic demand가 적을 때 소비 전력이 작게 된다.

ES 알고리즘을 사용했을 때 모든 경우의 수를 탐색하고 결과를 도출하기 때문에 가장 적은 전력을 소모하게 된다. 하지만 이 방법은 큰 컴퓨팅 소모를 하게 된다.

본 논문에서 제안한 THESIS 알고리즘이 네트워크의 traffic이 높을 때 0.4% 정도의 차이를 보이고, traffic이 낮을 때는 3.5%의 차이를 보임을 확인할 수 있다.

- 트래픽이 높을 때는 cell을 끌 필요가 많이 없고, 트래픽이 낮을 때 cell을 끌 기회가 많기 때문에 이러한 차이를 보인다.
- 트래픽이 높을 때 THESIS와 ES 알고리즘이 거의 비슷한 결과를 보이는 것으로 보아 트래픽이 높을 때는 ES 알고리즘의 high search space라는 장점은 퇴색된다.

그림 2(b)에서 ES 알고리즘은 사용되지 않았다. 컴퓨팅 오버헤드가 너무 커서 계산할 수가 없었기 때문이다.

![Figure.6](/assets/img/blog/lightweight-cs/figure.6.png)

최종적으로 위의 그래프에서 각 알고리즘 별 탄소 절감량을 확인할 수 있다. SBS 수가 늘어날수록 탄소 절감량도 늘어나게 되는데, SBS를 끌 수 있는 기회가 많아지기 떄문이다.

![Figure.7](/assets/img/blog/lightweight-cs/figure.7.png)

위의 그래프에서 각 알고리즘의 시간 복잡도를 확인할 수 있다. ES 알고리즘은 SBS의 수가 20개가 넘어가면 기하급수적으로 시간 복잡도가 증가하고, THESIS도 증가하는 추세를 보인다. 하지만 THESIS 알고리즘은 클러스터 내 SBS의 수를 $$B_{th}$$로 제한을 두기 때문에 시간복잡도가 기하급수적으로 증가하는 것을 막을 수 있다.

## 2.7 Conclusion

위에서 볼 수 있듯이 본 논문에서 제안한 THESIS 알고리즘은 획기적인 에너지 절감을 실현시킬 수 있다. 또한 AAO 알고리즘에 비해 탄소 절감을 늘릴 수 있다는 장점도 존재한다. 본 논문의 저자는 future work로 UAV-BS를 이용한 traffic offloading을 제안하였다.

# 3. Take Away

- Clustering + heuristic 알고리즘을 이용한 lightweight cell switching
- SBS 두 개를 합쳐 MBS로 가정하는 방법으로 HetNet 시뮬레이션