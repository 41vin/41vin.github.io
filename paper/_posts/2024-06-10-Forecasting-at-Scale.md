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

- Flexibility: 

# 3. Take away


