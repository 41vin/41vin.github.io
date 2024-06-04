---
layout: page
title: Basic Concept
description: >
    모델 경량화 관점에서 AutoML의 영역 이해
hide_description: true
sitemap: false
---
Keyword: Hyperparameter optimization
{:.note}

# AutoML Pipeline

## 일반적인 AutoML Pipeline

![automl-pipeline](/assets/img/docs/automl/automl03.png)

일반적인 AutoML 파이프라인은 아래와 같이 이루어진다.

1. Configuration $$\lambda$$ 설정 (백본, 하이퍼파라미터 등)
2. Train with $$\lambda$$ (학습이 잘 되는지는 Black box에 해당)
3. Evaluate Objective $$f(\lambda)$$ (목적 함수는 상황에 맞게 요청)
4. Blackbox optimization: Objective 함수 maximize

### AutoML Pipeline 예시: Bayesian Optimization(BO)

![bayesian-optimization](/assets/img/docs/automl/automl04.png)

Bayesian Optimization을 이용해 blackbox optimization이 2가지 과정으로 이루어진다.
- Update Surrogate Function ($$f(\lambda)$$를 예측하는 regression model)
- Update Acquisition Function (Surrogate Function을 사용해서 다음 시도해 볼 $$\lambda$$를 결정하는 model)


## Bayesian Optimization(with Gaussian Process Regression)
![BO](/assets/img/docs/automl/automl05.png)

Bayesian Optimization의 과정을 단계별로 설명하면
- 점선: Objective function 값
- 실선(파란색): Surrogate model의 값
- 초록 영역: Acquisition function의 값
  

탐색 과정은 아래와 같은 순서가 반복된다.
1. (n=2) acquisition max가 가장 높은 곳이 다음 탐색 가능 지점이 됨
2. (n=3) 학습을 수행하고 n=2일 때 가능성의 실제 값을 취함
3. (n=3) surrogate model update, acquisition function update
4. (n=3) acquisition max가 가장 높은곳이 다음 탐색 가능 지점이 됨

### Gaussian Process Regression

Gaussian Process Regression은 불확실성을 모델링할 수 있다는 장점이 있다. 불확실성을 모델링 한다는 것은 (n=2)일 때 우리가 알고 있는 점은 2개 밖에 없지만 그 사이의 값을 불확실성을 가지고 표현할 수 있다는 것이다.

기본 아이디어에 대해 설명
- 일반적인 Regression task의 문제 정의: "데이터를 가장 가깝게 피팅하는 함수 $$f$$를 찾자!"
- 학습 데이터: $$(X, Y)$$, 테스트 데이터: $$(X_{*}, Y_{*})$$, $$Y \approx f(X) + e$$
- 우리가 알고자 하는 특정 위치의 $$Y_{*}$$ 값은 우리가 알고 있는 $$X, Y, X_{*}$$들과 연관이 있지 않을까?

따라서 $$X, Y, X_{*}$$ 값으로부터 $$Y_{*}$$을 추정하는게 Gaussian Process Regression이고, 연관에 대한 표현은 kernel 함수 $$K$$로 표현한다.


### Surrogate Model(Function)

![Surrogate Model](/assets/img/docs/automl/automl06.png)

TBD

*[FLIP]: First Last Invert Play
