---
layout: page
title: AutoML Overview
description: >
    모델 경량화 관점에서 AutoML의 영역 이해
hide_description: true
sitemap: false
---
Keyword: Hyperparameter optimization
{:.note}

# 좋은 모델과 파라미터 찾기: AutoML 이론

## Conventional DL Training Pipeline

데이터 엔지니어링이라 불리는 것들

1. Data Cleansing, Preprocessing
2. Feature Engineering
3. Select ML Algorithm
4. Set Hyperparameters
5. ...

이 중 Deep learning에서 ML 알고리즘 선택과 하이퍼파라미터 세팅에 
- Select backbone model(ResNet, MobileNet, EfficientNet, ...)
- Hyperparameters(Loss, Optimizer, Learning rate, Batchsize, ...)

가 사용된다.

![automl01](/assets/img/docs/automl/automl01.png)




AutoML이 실현하고자 하는 것은 "True" end-to end ML이다.


## Objectives of AutoML
AutoML(HPO: Hyperparameter Optimization)의 문제 정의

![automl01](/assets/img/docs/automl/automl02.png)

어떤 학습 데이터와 검증 데이터가 주어지고, 알고리즘과 하이퍼파라미터가 정의되어 있을 때 Loss를 가장 최소화해주는 하이퍼파라미터를 찾는 것.

앞에서 언급한 것처럼

사람이 해야하는 것을 대신 해줌으로써 진정한 End-to-end 러닝을 실현한다.
{:.note}

## 딥러닝의 configuration

### 주요 타입 구분
A. Categorical
- ex. optimizer:[Adam, SGD, AdamW, ...], module: [Conv, BottleNeck, InvertedResidual, ...]

B. Continuous
- ex. learning rate, regularizer param, ...

C. Integer
- ex. batch_size, epochs, ...

또한 딥러닝 configuration을 어떻게 설정하냐에 따라 탐색 공간이 달라질 수 있다는 조건부 특징이 있다. Adam을 사용한다면 alpha, beta1, beta2의 값들을 추가로 설정해야 한다.


## 모델경량화 관점에서의 AutoML

1. 기존 가지고 있는 모델을 경량화 하는 기법
   - Prunning, Tensor decomposition, ...
2. Search를 통하여 경량 모델을 찾는 기법
   - NAS(Neural Architecture Search), AutoML(Automated Machine Learning), ...
  
*[FLIP]: First Last Invert Play
