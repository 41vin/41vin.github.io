---
layout: post
title: (IEEE'22)OpenRAN Gym-AI/ML Development, Data Collection, and Testing for O-RAN on PAWR Platforms
tags: [o-ran, ieee]
description: >
  **Authors**: Leonardo Bonati, Michele Polese, Salvatore D'Oro, Stefano Basagni, Tommaso Melodia
sitemap: false
hide_last_modified: true
---

Open RAN, AI/ML, SDK
{:.note title="Keywords"}

# 1. Why this paper

- `MaveRIC` 시뮬레이터에 쓰일 알고리즘 공부
- RIC에 쓰일 알고리즘 구상에 필요한 프로그램

# 2. Summary of paper

## 2.1 Introduction
`Open RAN`은 네트워크 운영자들이 하드웨어 인프라를 교체하지 않고 네트워크 컨디션과 사용자의 요구에 맞춰 맞춤형 서비스를 제공할 수 있게 한다. 또한 하드웨어 교체가 필요없기 때문에 Operational cost를 효과적으로 감소시킬 수 있다.

O-RAN Alliance는 Open RAN을 O-RAN으로 브랜딩하여 표준을 개발중이다. 
{:.note title="Attention"}

O-RAN은 두 개의 RAN Intelligent Controller들을 제안하였다.
1. `near-RT RIC` 
   - RAN 구성요소들(CUs, DUs)들과 `E2` 인터페이스로 연결되어 있으며 10ms와 1s 사이에서 작동하는 control loop들을 담당한다.
2. `non-RT RIC` 
   - Service Management and Orchestration(SMO) 프레임워크들을 포함하고 있으며 1s보다 큰 시간 스케일에서 작동한다. 
   - 하나 혹은 여러개의 near-RT RIC들과 `A1` 인터페이스를 통해 연결되어 있으며 이를 통해 policy들과 네트워크 외부 정보들을 전파시킨다.
   - RIC 위에 올라가는 AI/ML 모델을 관리한다.

`SMO`는 RAN과 `O1` 인터페이스를 통해 연결되며 management와 orchestration을 한다. 또한, virtualization platform(`O-cloud`)과 `O2` 인터페이스를 통해 연결되어 있다.

AI/ML 알고리즘들을 test/design 하는 것은 다음과 같은 단계들을 포함한다.

1. **Data collection**: AI/ML 모델의 배포될 상황과 다양한 네트워크의 performance indicatos에 맞춰 practical한 dataset을 수집
2. **AI/ML model design**: 모델의 입/출력을 결정하고, training과 teseting을 결정 후, 성능 평가
3. **Model deployment**: xApp은 near-RT RIC에 rApp은 non-rt-RIC에 배포
4. **Model fine-tuning**: RAN의 런타임 데이터에 맞춰 모델을 서로 다른 프로덕션 환경에 적응시킴
5. **Control, inference and/or forecasting**: RAN을 AI/ML을 통해 제어


본 논문에서는 **OpenRAN Gym**(O-RAN 규격을 준수하는 inference와 control AI/ML 알고리즘을 개발하기 위한 오픈소스 toolbox)을 제시하고 있다.
{:.note}

저자는 가장 먼저 OpenRAN Gym의 high-level overview를 제공하고 그 구성요소가 어떻게 data-driven xApp들의 개발, 테스트 workflow들을 실현시키는지 논의한다.

OpenRAN Gym은 end-to-end 디자인, prototyping, testing, 그리고 HetNet 구조에서 실험된 최초의 오픈된, portable한 툴셋이다.

**OpenRAN Gym의 contribution**: O-RAN ecosystem에서 AI/ML 솔루션들의 end-to-end 디자인과 테스트를 가능케함

---

## 2.2 OpenRAN Gym

### 2.2.1 주요 구성요소
1. 공식적, 원격으로 접속 가능한 HetNet 환경에서의 *experimental wireless platforms*, `Colosseum` 
2. Open 프로토콜 스택으로 구현된 *softwarized* RAN, `srsRAN`, `OAI`
3. *Data collection and control framework*, `SCOPE`
4. *O-RAN control architecture*, `ColO-RAN`


- Colosseum
  - 세계에서 가장 큰 네트워크 에뮬레이터. 연구자와 기술자들이 거대한 스케일의 환경에서 연구를 진행할 수 있게 한다. 

- POWDER
  - Salt Lake City에 있는 도시 규모의 wireless tesetbed

- COSMOS
  - New York City에 있는 도시 규모의 wireless testbed. mmWave 통신과 edge-computing을 중점으로 구현하였다.

![Figure 1. OpenRAN Gym architecture](https://bl6pap003files.storage.live.com/y4mf7YSUL26iumwPZ3h55hIdDO858VACSSNVFA5SX6PKA8bkJjJhNHWGnMP9-072e9U5qTgHsYfoV60orvCXHzcyn3OqLt2WB2Ux7E3ht0P4VrmoOO9aFpX1ptdNDcEt9_xsnghW-xZHOr4POnSUiR3-4236bNBXtGdMA6TYnfFlrAtiOW1dKm45DD3ODnay4jDdFiwfkr59KguyaCrRAFSNVWbnrzjycvJg9-yolerkYM?encodeFailures=1&width=931&height=308)

글이 작성된 시점에 OpenRAN Gym은 srsRAN을 이용해 softwarized RAN을 구성하였다. 또한, cellular protocol stack은 SCOPE 프레임워크에 의해 지원되었다. SCOPE를 사용하여 HetNet 환경에서의 데이터 수집을 자동화할 수 있다.

최종적으로, ColO-RAN을 사용해 O-RAN control architecture를 구현하였다. ColO-RAN은 사용자들이 AI/ML 기반 어플리케이션들을 xApp SDK를 사용해 prototype 할 수 있게 한다.

---

## 2.3. DATA Collection and Control Framework

OpenRAN Gym의 데이터 수집과 control 프레임워크는 SCOPE에 기반한다.
{:.note}

RAN의 런타임에 fine-tune과 데이터 수집을 담당하는 Open API들은 SCOPE에 의해 제공된다.
SCOPE는 실제 실행되고 있는 HetNet 실험환경에서 RAN KPM들을 수집하는 과정을 촉진시킬 수 있다. 결과적으로, SCOPE는 OSC DU에 있는 O-RAN E2 termination을 O-RAN near-RT RIC과 연결해준다.


### 2.3.A *Starting SCOPE*
SCOPE는 configuration 파일을 통해 cellular base station들을 작동할 수 있는 Command-line Interface (CLI) tool들을 지원한다.OpenRAN Gym에서 사용하는 주요 파라미터들은 다음과 같다.

- `network-slicing`: base station에서 network slicing을 가능하게 함
- `slice-allocation`: network slicing이 가능하다면, Resource Block Groups(RBGs)를 세팅하는 파라미터
  - **예시**: {0:[0, 5], 1:[6, 10]}의 뜻은 RBG 0-5는 slice 0에 6-10은 slice 1에 할당
- `slice-scheduling-policy`: 기지국의 network slice에 scheduling policy를 세팅
  - **예시**: [1, 2]: slice 0에 slicing policy 1울 헐덩, slice 1에 policy 2를 할당(0: round-robin, 1: waterfilling, 2:proportionally fair)
- `slice-users`: UE들을 특정 network slice에 associate 시킴.
  - **예시**: {0:[4, 5], 1:[2, 3]}, UE 4,5를 slice 0, UE 2,3을 slice 1에 associate
- `generic-testbed`: SCOPE가 Colloseum 이외의 테스트베드에서 실행되고 있는지 여부를 나타냄

`SCOPE` 구성 파일이 JSON 형식 파일(*radio.conf*)로 작성된 후 cellular base station, core 네트워크, UE 어플리케이션들은 아래의 커맨드를 통해 시작된다.

``` bash
# !/bin/bash
cd radio_api/
python3 scope_start.py --config-file radio.conf
```

런타임 중 RAN으로부터 온 Relevang KPM들은 자동적으로 SCOPE base station들에 로깅된다. KPM 파일들은 CSV 형식으로 저장되고, on-the-fly로도 사용될 수 있다.(AI/ML 모델 훈련). 또한 experiment가 끝나고 offline으로도 회수되어 사용할 수 있다.


## 2.4 O-RAN Control Architecture
OpenRAN GYM에서 사용하는 O-RAN control architecture는 ColO-RAN에 기반한다. 이 프레임워크는 OSC near-RT RIC의 lightweight 구현을 제공한다. Colosseum 시스템에서 standalone Docker 컨테이너로 구현되기 때문이다.

ColO-RAN의 high level 다이어그램은 아래의 그림과 같다. 
![Figure 2. ColO-RAN xApp](https://bl6pap003files.storage.live.com/y4m3-f1XORYznNVC_I8Ayg_mylORL4nUYqx-ZYoR6hz9_I_v6vAMYw5WrojtP06WFqQ9R9S-qoK6m_abJw7B7Ot1dYmzfiObA5OR9W0m0Q6WuuGGma-HC0L46if_WEg0zTDTmZTR4BFHPMBoosS98-HNTd2YePVPjsgB2cDvlNKKL2mJ_TsgLwlryCTUcnBkrtv1c4SFwz1LmSgq-bTvFopK52Bw-e5Ad1cMqelp1XUMKw?encodeFailures=1&width=501&height=233)

ColO-RAN xApp은 두개의 주 구성요소로 이루어져있다,

- *Service Model(SM) Connector*: xApp과 near-RT RIC 사이의 메세지들을 관리
- *Data-driven logic unit*: RAN base station들로부터 받은 KPM들은 처리하고 AI/ML 모델들로부터 받은 과제 수행
  - **AI/ML model**: 특정 data-driven model로 구성(예, DRL agent, DNN 등)
  - **Data processing module**: KPM 데이터들을 AI/ML 모델이 사용할 수 있는 데이터 형식으로 변환 

### 2.4.A Starting the ColO-RAN Near-RT RIC
ColO-RAN의 near-RT RIC은 `setup-ric.sh`를 이용하여 docker 컨테이너로 빌드될 수 있다. 이 스크립트를 이용해 RIC은 RAN으로부터 메세지를 받고 교환할 수 있다.
``` bash
#!/bin/bash
cd setup-scripts/
./setup-ric.sh col0
```

ColO-RAN near-RT RIC의 이미지들은 다음과 같은 항목들을 포함한다.
1. `e2term`: RIC E2 메세지의 종단점
2. `e2mgr`: E2 인터페이스로 송/수신되는 메시지들을 관리
3. `e2rtmansim`: RIC 안에서 RIC Message Router(RMR) 프로토콜을 사용하여 E2 메시지들을 라우팅
4. `db`: Redis 데이터베이스를 이용해 RIC에 연결된 RAN 노드들의 기록을 저장

Docker 이미지 빌드 후, E2 termination 종단점을 통해 다가올 연결을 listening하는 RIC 컨테이너들이 생성된다. 

### 2.4.B Connecting the SCOPE Base Station to ColO-RAN

위의 과정을 통해 ColO-RAN이 세팅되고 시작된 후, cellular base station은 O-RAN E2 termination을 통해 연결될 수 있다. RAN-side E2 termination은 아래의 목적들로 사용될 수 있는데
1. xApp들로부터 *RIC Subscription* 수신
2. *RIC Indication* 메세지를 통해 xApp들에게 주기적으로 KPM reports를 송신
3. xApp들로부터 받은 control message들에 기반해 base station들의 런타임 동안 configuration을 수정

SCOPE base station의 E2 termination을 초기화하는 커맨드는 다음과 같다.

``` bash
#!/bin/bash
cd colosseum-scope-e2/
./build_odu.sh clean
./run_odu.sh
```
- `build_odu.sh`: near-RT RIC이 연결할 IP 주소와 포트를 특정
- `run_odu.sh`: E2 termination 프로세스 실행, base station과 near-RT RIC 연결

연결이 성공적으로 수행되었다면 `docker logs -f e2term` 커맨드를 통해 확인할 수 있다.

### 2.4.C Initializing a Sample xApp

SCOPE base station이 near-RT RIC에 연결된 후, xApp들은 시작될 수 있다. 본 논문에서는 xApp 디자인을 장려하기 위해 `ready-to-use sample xApp` 템플릿을 제공하고 있다. 이 템플릿에 연구자들은 AI/ML 모델을 plug-in 하기만 하면 된다.

``` bash
#!/bin/bash
cd setup-scripts/
./setup-sample-xapp.sh gnb:<example>
```

xApp이 구독해야하는 base station의 식별자를 커맨드에 입력한다. 그 후 sample xApp의 Docker 이미지를 빌드한다. 스크립트는 near-RT RIC에서 sample-xApp이라는 xApp Docker 컨테이너를 시작한다.

컨테이너 시작 후, xApp 프로세스들은 아래의 커맨드들을 통해 실행된다.
``` bash
#!/bin/bash
docker exec -it sample-xapp /home/sample-xapp/run_xapp.sh
```

위의 커맨드는 targeted RAN 노드들에 RIC Subscription 메시지, 주기적인 RAN KPMs reports를 통해 xApp subscription을 유발한다.

## 2.5 xApp Development Workflow on Colosseum

Open RAN Gym on Colosseum을 사용해 Data-driven xApp을 개발하는 과정은 아래의 그림과 같이 나타낼 수 있다.

![OpenRAN Gym xApp Design and testing workflow on Colosseum](https://bl6pap003files.storage.live.com/y4m101-BVwvePNVHm_3mjDTib8K-_UU4uv5VFr8H-epdKVuYkYzOuwW8mBBn2NELUwiPWSbqccAd9SZIUW_gDstPl2GL9xvLLhgNXrDGHlw6c31vqAUhkjgotll-e9pUNpnkqrITpfrZL8-Tt1b5jR5KxIV1F-RlfletrIDKeicDXAS-dBfUh1pYuJSlBS6hlYnqMIiU4hrwMLHYZWnxAKnjQgYie9T28sHpDvrXG26hP0?encodeFailures=1&width=1080&height=312)

1. **Data collection**
   
   xApp에 임베드 되어있는 AI/ML 모델을 train/test하기 위한 데이터 수집

2. **Model design, training and testing**

    AI/ML 모델이 사용할 알고리즘 선택, input으로 사용할 데이터 선택, output 액션들의 집합 설정을 포함한다. 디자인 phase 이후, offline으로 모델은 학습되고 테스트된다.

3. **Deploy the model as an xApp**
   
    모델 test 후, 