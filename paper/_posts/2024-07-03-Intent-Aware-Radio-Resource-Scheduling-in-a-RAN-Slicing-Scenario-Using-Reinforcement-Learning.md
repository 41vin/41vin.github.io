---
layout: post
title: Intent-Aware Radio Resource Scheduling in a RAN Slicing Scenario Using Reinforcement Learning
tags: [ieee]
image: /assets/img/blog/ran-slicing/RAN-AI.png
description: >
  **Authors**: Sihem Bakri et al.
sitemap: false
hide_last_modified: true
---

Radio resource scheduling, RAN slicing, intent-aware sheduling, reinforcement learning
{:.note title="Keywords"}


* this unordered seed list will be replaced by the toc
{:toc}



# 1. Why this paper
- 머신러닝을 활용한 RAN Slicing 프레임워크 이해
- `Intent-driven` 개념 설명
- Simulation 코드가 존재함

---

# 2. Summary of paper

## 2.1 Introduction

5G 시스템은 서로 다른 `network slice`의 구성과 구조를 slice들의 요구에 맞춰 배치할 수 있고 기능들을 online으로 바꿀 수 있다.

End-to-end network slicing: RAN + Transport network + Core network
{:.note}

- `inter-slice scheduling`: `RAN slicing`의 기능 중 하나는 slice 인스턴스들간의 무선 자원들을 배치하는 Radio Resource Scheduling(RRS)를 배포하는 것이다.

- `intra-slice scheduling`: 다른 RRS는 slice 인스턴스 내에 있는 자원들을 담당한다.

위에서 설명한 inter, intra-slice RRS scheduler들은 반드시 slice들의 intent들을 만족시키며 무선 자원을 분산시켜야 한다.

새로운 RRS는 아래의 조건들을 만족시켜야 한다.

1. 서로 다른 slice 종류들의 다른 요구사항들을 만족시킬 것
2. 각 slice에 충분한 무선 자원을 제공하여 네트워크 intent를 만족시킬 것
3. 모든 slice들의 요구사항을 만족시킬만큼의 무선 자원이 없을 때, 가장 중요한 slice intent를 우선시할 것

RAN은 데이터가 풍부한 환경을 가지고 있기 때문에 `data-driven` 접근법이 큰 각광을 받고 있다. 특히, 강화학습은 환경에 상관없이 유연한 결정을 정책을 실현시킬 수 있어 큰 주목을 받고 있다.

알고리즘만큼 중요한 것이 `Autonomous RRS system`이다. `Autonomous RRS system`은 서로 다른 slice들로부터의 요구사항들을 만족시킬 수 있다. 특히 본 논문은 `TM Forum`에서 정의한 네트워크 요구사항들을 이용해 intent를 구체화시키고 있다.

RRS는 다른 슬라이스 intent들을 받고 네트워크가 이 intent들을 만족시킬 수 있게 변화시켜야 한다.

본 논문은 강화학습을 사용한 RAN slicing을 위한 intent-aware RRS를 제안하고 있다. 이 RRS는 서로 다른 slice 종류들과 QoS intent(throughput, latency, 그리고 packet loss rate에 기반한)를 사용한다.
{:.note}

저자가 제안하는 방법을 크게 나눠보면
1. `inter-slice` allocation: RL agent
2. `intra-slice` allocation: RRS가 `round-robin` 스케쥴러를 이용

으로 정리할 수 있다.

RL agent는 
1. slice들의 intent들을 만족시킬 수 있는 RRS의 action들
2. SLA에 정의되어 있는 weight들에 따라 slice intent의 priority를 

학습한다.


---


# 3. Take Away

- 논문에서 사용한 채널 시뮬레이터 `QuaDRiGa` 대신 ns-3로 구현해보기
