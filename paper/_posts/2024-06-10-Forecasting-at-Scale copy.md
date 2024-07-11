---
layout: post
title: Forecasting at Scale
tags: [tp, stat]
image: /assets/img/blog/prophet/prophet_logo.webp
description: >
  **Authors**: Attai Ibrahim Abubakar, Hichael S. Mollel, Metin Ozturk, Sajjad Hussain, and Muhammad Ali Imran
sitemap: false
hide_last_modified: true
---

Time Series, Statistical Practice, Nonlinear Regression
{:.note title="Keywords"}


* this unordered seed list 􏰁ill be replaced by the toc
{:toc}


# 1. Why this paper
- 통계 기반 시계열 예측 알고리즘 조사
- SKT AI Fellowship 비교 알고리즘 개발에 필요

# 2. Summary of paper

## 2.1 Introduction


- 비즈니스 단계에서 높은 수준의 시계열 예측이 어려운 이유
  1. 완전 자동화된 예측 기술들은 튜닝하기 어렵고, heuristic에 비해 유연하지 않음
  2. 조직 내 데이터 전문가들이 시계열 예측에 대해 익숙하지 않을 수 있음

규모에 상관없는 forecasting을 만들기 위한 유용한 가이드라인들을 제공하는 것이 목적임
{:.note}

본 논문에서는 큰 스케일의 시계열 트래픽을 예측하는 것이 상대적으로 간단하다고 한다. 병렬화가 매우 용이하며 예측 결과를 관계형 데이터베이스에 저장하는 것도 어렵지 않기 떄문이다. 진짜 큰 어려움은 **예측 결과의 신뢰성**이다.

![Figure.1](/assets/img/blog/prophet/prophet1.png)

1. 각 parameter가 쉽게 이해될 수 있도록 유연한 방법으로 시계열을 모델링
2. 모델과 기준 모델에 대해 다양한 과거 데이터에 대한 예측을 생성 후, 성능 평가
3. 성능이 좋지 않을 경우, 우선순위를 정해 문제를 전문가에게 알림
4. 분석가가 이를 검토하고 피드백을 바탕으로 모델을 조정


## 2.2 Features of Business Time Seriers

![Figure.2](/assets/img/blog/prophet/prophet2.png)

위의 그림은 **R**의 **forecasting** 패키지를 이용해 다양한 알고리즘으로 예측을 수행한 것이다.
{:.note}

전체 시간 중 세개의 구역에서 예측을 수행하였으며, 각각의 예측은 시작 지점 전까지의 시계열 데이터만을 이용하여 해당 날짜에 예측을 수행하는 시뮬레이션을 한 것이다. 예측에는 아래의 알고리즘들이 사용되었다.

- `auto.arima`
  - ARIMA 모델 여러개 중 가장 성능이 좋은 모델을 고름
  - Large trend error에 취약
- `ets`
  - 여러 개의 exponential 함moothing model 중 가장 은능이 좋은 모델을 고름
  - 주간 trend 데이터의 계절성은 잘 포착하지만, 장기간 trend 계절성은 잘 포착하지 못함
- `snaive`
  - 계절성을 가지는 데이터를 예측하는 데 사용되며, 매주 반복되는 패턴을 가진 데이터에 적합함
  - 주간 trend 데이터의 계절성은 잘 포착하지만, 장기간 trend 계절성은 잘 포착하지 못함
- `tbats`
  - 주간 혹은 연간 계절성을 가지는 데이터에 적합함

예측이 잘못되었을 때 모델의 parameter들을 튜닝해야 하는데 이 과정에서 시계열 모델에 대한 깊은 이해가 필요함
{:.note}


## 2.3 The Prophet Forecasting Model

Harvey & Peters가 제안한 방법을 활용하여 시계열 데이터를 세가지 주요 컴포넌트로 분류하였다: trend, seasonality, and holidays. 아래의 식과 같이 나타낼 수 있다.

$$ y(t) = g(t) + s(t) + h(t) + \epsilon_{t} $$

- $$g(t)$$ : 시계열 데이터에서 비주기적인 변화를 모델링하는 trend 함수
- $$s(t)$$ : 주기적인 변화(e.g., weekly 혹은 yearly 계절성)를 나타냄
- $$h(t)$$ : 하루 혹은 그 이상으로 불규칙적으로 스케쥴링 된 날들을 모델링하는 holiday 함수
- $$\epsilon_{t}$$ : 모델에 캡쳐되지 않는 독특한 변화들, 후에 normally 분포되었다고 가정할 예정

이러한 구조는 Hastie & Tibshiran이 제안한 Generalized Additive Model(GAM)과 유사하다

> Non-linear smoothers가 regressor에 적용된 regression model의 한 종류

여기서는 시간만을 regressor로 사용하지만, 시간의 여러 선형 및 비선형 함수들을 구성 요소로 사용할 수 있다.

GAM 공식은 분해하기 쉽고 필수적으로 새로운 컴포넌트들을 포함한다는 장점이 있다. 또한 매우 빠르게 환경에 적응한다. 따라서 사용자들은 모델 parameter들을 이에 맞춰 변화시킬 수 있다.

본 논문의 저자들은 예측 문제를 **curve-fitting exercise**로 정의하였다. 이는 데이터의 시간 의존 구조를 고려하는 시계열 모델과 본질적으로 다르다.

ARIMA와 같은 생성 모델을 이용하면 얻을 수 있는 inferential 장점들을 포기해야 하지만 아래와 같은 practical한 장점들을 얻을 수 있다.

- Flexibility: 다양한 기간의 계절성을 쉽게 반영할 수 있으며, 분석가가 trend에 대한 다양한 가정을 할 수 있게 한다.
- ARIMA와 달리 측정값이 규칙적으로 분포될 필요가 없으며, 결측값을 보간할 필요 없다.
- Fitting 속도가 빠르다.
- 해석하기 쉬운 파라미터들을 가지고 있어 분석가가 다루기 용이하다.

### 2.3.1 The Trend Model

본 논문에서는 두가지 trend 모델을 설명하고 있는데,

1. Nonlinear, Saturating Growth
2. Linear Trend with Changepoints

가 해당한다.

#### 2.3.1.1 Nonlinear, Saturating Growth

Growth 예측에 있어 데이터 생성 프로세스의 중요 구성요소는 population이 어떻게 증가하였고 증가할 것인지를 모델링하는 것이다. 주로 logistic growth 모델을 사용하여 이러한 종류의 growth를 모델링한다.

$$ g(t) = \frac{C}{1 + \exp(-l(t - m))} $$

- $C$: carrying capacity
- $k$: growth rate
- $m$: offset 파라미터

하지만 Facebook이 포착해내지 못한 두가지 요소가 있다.
1. Carrying capacity는 일정하지 않다.
   - 해결책: $$C \rightarrow C(t)$$
2. Growth rate도 일정하지 않다.
   - 해결책: $$k$$를 대체하는 많은 products



- Growth 모델 안에 있는 trend changes를 포함하기 위해 changepoints를 정의하였다.
1. 시간 $$s_j$$ 안에 $$S$$개의 change point
2. Rate adjustment 벡터: $$\delta \in \mathbb{R}^{S}$$   
   - Adjustment의 식:  $$k+\sum_{j:t>s_j}\delta_{j}$$
   - 벡터 $$a(t) \in {0, 1}^{S}$$를 이용해 식을 명확하게 나타내면

$$
a_j(t) = 
\begin{cases} 
1, & \text{if } t \geq s_j, \\ 
0, & \text{otherwise}.
\end{cases}
$$


시간 $$t$$의 rate는 $$k+a(t)^{\intercal}\lambda$$가 된다. Rate $$k$$가 결정되었을 때, offset 파라미터 $$m$$도 각 세그먼트들의 endpoint들을 잇기 위해 조정된다.

- Changeoint $$j$$에서의 correct adjustment는 아래와 같이 정의된다. 

$$
\gamma_j = \left(s_j - m - \sum_{l<j} \gamma_l \right) \left(1 - \frac{k + \sum_{l<j} \delta_l}{k + \sum_{l \leq j} \delta_l}\right)
$$

- 각 부분의 logistic growth 모델도 다음과 같다.

$$
g(t) = \frac{C(t)}{1 + \exp \left( -(k + a(t)^{\top}\delta) \left( t - (m + a(t)^{\top}\gamma) \right) \right)}
$$

본 논문의 모델에서 중요한 파라미터의 집합은 $$C(t)$$이다. 데이터에 인사이트가 깊은 분석가들은 데이터 특성에 맞춰 $$C(t)$$를 설정하곤 한다.

#### 2.3.1.2 Linear Trend with Changepoints

Saturating growth를 가지고 있지 않은 forecasting 문제들, 즉 constant rate of growth는 간단하고 유용한 모델을 제공한다.

$$ g(t) = (k + a(t)^{\intercal}\lambda)t + (m + a(t)^{\intercal}\gamma) $$

이 때 $$\gamma_{j}$$는 function을 연속적으로 만들기 위해 아래와 같이 설정된다.

$$\gamma_{j} = -s_{j}\lambda_{j}$$

#### 2.3.1.3 Automatic Changepoint Selection

분석가들은 프로덕트가 시작된 날짜, 다른 growth-altering 사건들에 대한 정보를 이용해 changepoint $$s_j$$를 결정한다. $$s_j$$의 automatic selection은 $$\delta$$에 sparse prior을 둠으로써 자연스럽게 이루어진다.

다수의 changepoint들을 특정할 때 $$ \delta_{j} \sim Laplace(0, \tau) $$를 사용한다.
- $$\tau$$: 직접적으로 모델의 flexibility를 조절

Adjustment $$\lambda$$의 spare prior는 primary growth rate $$k$$에 영향을 미치지 않기 때문에, $$\tau$$가 0이 되면 모델은 단순한 logistic 또는 linear growth 모델로 변환된다. 

#### 2.3.1.4 Trend Forecast Uncertainty

예측의 불확실성을 생성 모델을 이용해서 측정한다. Trend를 위한 generative 모델은 $$T$$개의 데이터 포인트로 구성된 과거 기록에서 $$S$$개의 변화점이 있으며, 각 변화점은 $$\lambda_{j} \sim Laplace(0, \tau)$$의 분포를 가진다.


미래의 rate 변화를 과거의 데이터에서 $$\tau$$를 바꿔 에뮬레이션한 값으로 대체하며 시뮬레이션을 진행한다. Bayesian 프레임워크에서는 $$\tau$$의 hierarchical prior을 이용해 posterior를 구해 진행할 수 있다. 다른 방법으로는 rate scale 파라미터: $$\lambda = \frac{1}{S} \sum_{j=1}^{S} \|\delta_j\|$$ 의 maximum likelihood estimate를 사용할 수 있다.


미래의 changepoint들도 과거 chagepoint들의 평균 frequency에 맞춰 랜덤하게 샘플된다.

$$
\forall j > T,
\begin{cases}
\delta_j = 0 & \text{w.p.} \ \frac{T - S}{T}, \\
\delta_j \sim \text{Laplace}(0, \lambda) & \text{w.p.} \ \frac{S}{T}.
\end{cases}
$$

미래의 trend가 과거의 trend를 따를것이라는 가정은 강력한 가정이기 때문에 예측에 실패한 구간을 만들 수도 있다.

$$\tau$$가 증가할수록 모델의 과거 데이터를 더 유연하게 맞출 수 있어 training error가 줄어든다. 그러나 이 flexibility로 인해 예측에 실패한 구간이 넓어질 것이다.


### 2.3.2 Seasonality

비즈니스 시계열 데이터는 사람들의 행동 패턴에 따라 계절성을 가지게 된다. 이 계절성을 예측하기 위해 정확하게 계절성 데이터를 모델링해야 한다. 본 논문에서는 periodic effects에 flexible 모델을 제공하기 위해 `Fourier series`를 사용한다.

Arbitrary smooth seasonal effects를 다음과 같이 모델링할 수 있다.

$$
s(t) = \sum_{n=1}^{N} \left( a_n \cos \left( \frac{2 \pi n t}{P} \right) + b_n \sin \left( \frac{2 \pi n t}{P} \right) \right)
$$

- Fourier 계수 추정
  - Seasonal 패턴을 정의하기 위해 2N개의 parameter를 추정해야 한다.
  - 이를 위해 각 시간 $$t$$에 대한 행렬 $$X(t)$$를 구성한다.

- 계절성 계산
  - $$s(t) = X(t)\beta $$
    - $$\beta \sim Normal(0, \sigma^2)$$  

# 3. Take away


