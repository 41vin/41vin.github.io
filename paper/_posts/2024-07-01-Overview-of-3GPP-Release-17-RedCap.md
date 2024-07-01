---
layout: post
title: Toward Smaller and Lower-Cost 5G Devices with Longer Battery Life - An Overview of 3GPP Release 17 RedCap 
tags: [3gpp]
image: /assets/img/blog/RedCap/RedCap-Usecases.png
description: >
  **Authors**: Sandeep Narayanan Kadan Veedu et al.
sitemap: false
hide_last_modified: true
---

RedCap, 5G, NR
{:.note title="Keywords"}


* this unordered seed list will be replaced by the toc
{:toc}


# 1. Why this paper
- Network Slicing 논문의 구성요소인 RedCap 조사

# 2. Summary of paper

## REL-17 Standardization Work on RedCap UE

본 파트에서는 Rel-17 RecCap UE의 요구사항을 설명한다.

### A. Use Case Specific Requirements

Rel-17 RedCap UE는 세 개의 main usecase가 정의되어 있다.

- Industrial wireless sensors

- Video surveillance
- Smart wearable devices


![Table1](/assets/img/blog/RedCap/Table1.png)

### B. Objectives of Rel-17 W1 on RedCap UE

1. UE complexity reduction features

- **UE bandwidth**
  - Maximum UE 대역폭이 FR1에서 20MHz, FR2에서 100MHz로 감소
  - UE 대역폭을 감소시킨 RedCap UE와 normal UE의 coexistence가 특정 시나리오에서 system performance에 영향을 미칠 것임

- **Number of receiving antennas**
  - 최소한의 Rx 안테나수(1개)가 NR RedCap에서 허용됨
  - UE의 complexity가 감소될 것으로 예상

- **Number of DL MIMO Layers**
  - 1 Rx를 가지고 있는 RedCap UE에게 1 DL MIMO layer 지원
  - 2 RX를 가지고 있는 RedCap UE는 2 DL MIMO layer 지원

- **Modulation order**
  - FR1 DL에서 256QAM을 필수로 사용하는 것이 아닌 선택 사항으로 허용 (RedCap UE를 보다 유연하게 구현 가능)

- **Duplex mode**
  - 목표는 Rel-15/16의 기존 프로토콜을 활용하여 사양에 미치는 영향을 최소화 하는 것임
  - RedCap UE는 UL 및 DL 전송에 서로 다른 local oscillator를 활용함으로써 duplexing 프로세스를 효과적으로 관리하고 효율적인 통신을 보장함

UE의 최대 bandwidth를 40MHz로 늘리는 의견이 나왔지만, 대역폭을 늘리면 폼 펙터의 크기가 커지기 떄문에 기각됨.
{:.note}

2. Higher layer supported features

- **RedCap UE type**
  - Rel-17에서 한가지의 RedCap UE 타입이 필수 UE capability와 RRC parameter들과 정의됨.

- **Early indication**
  - Initial access procedure에서 Msg1과 Msg3을 통한 early indication이 지원됨.

- **DRX enhancement**
  - DRC cycle이 확장되어 길어진 sleeping time과 energy saving을 달성함.
  - 정확한 signaling procedure들과 자세한 구현들은 3GPP RAN2에 정의되어 있음.

- **RRM measurement relaxations**
  - Rel-17 RRM measurement relaxation들은 RedCap UE의 requirement들을 맞추기 위해 추가 연구가 필요함.

## Key Characteristics and Analysis for REL-17 RedCap UE

### A. Reduced RedCap Ue Bandwidth

RedCap UE의 bandwidth capability는 complexity 감소를 위해 대폭 절감되었다.

일반 UE와 RedCap UE가 공존할 때, 초기 UL/DL BWP가 RedCap UE의 대역폭보다 넓은 경우에 RACH 접속 기회나 최적의 SSB가 RedCap UE의 대역폭을 벗어나는 경우가 발생한다. 이로 인해 RedCap UE는 정상적인 네트워크 접속에 실패할 수 있다.

Bandwidth 불일치로 인한 문제를 최소화하기 위해 여러 방법이 제안되고 연구되고 있다. 즉 RedCap UE가 제한된 대역폭 내에서도 효율적으로 작동할 수 있도록 하는 연구가 진행되고 있다.[3GPP TSG RAN1#108, R1-2202535, RAN1 agreements for Rel-17 NR
RedCap[R], Ericsson, Nov.2021]

- **Method 1: Up to gNB implementation**
  - 초기에 회사들이 gNB의 resource configuration에 적절한 제약들을 걸어 bnadwidth의 불일치를 막는 방법을 제안함.
  - 필연적으로 RedCap UE와 일반 UE에게 performance loss를 가져옴.


![Figure1](/assets/img/blog/RedCap/Figure1.png)

- **Method 2: Proper RF retuning for RedCap UE**
  - RF retuning의 기본 아이디어는 frequency 포인트를 다른 frequency 포인트로 이동시키는 것임.
    - 이동시키는 과정에서 약간의 delay가 발생함.
    - 저지연이 필요한 RedCap UE에서 적절한 RF retuning을 쓰는 것이 중요함

- **Method 3: Separate initial UL/DL BWPs for RedCap UE**
  - 가장 효과적인 방법은 initial UL/DL BWP와 RedCap UE의 BWP를 분리시키는 것임.
  - SSB와 전체 CORESET#0을 별도의 초기 DL BWP에 포함시킬지 여부, TDD에서 초기 UL 및 DL BWP의 중심 주파수가 다른 경우를 지원할지 여부 등 몇 가지 논란이 존재하였음
    - Rel-17에서 specification impact와 system performance 사이의 균형을 고려하여 최종적으로 내려짐.

TDD에서 UL과 DL의 BWP center frequeny는 동일하게 설정되어야 한다. Rel-17에서 TDD의 UL과 DL center frequency가 다르다는 직접적인 언급은 없지만 다르게 설정한다면 표준에 큰 영향을 미치고 구현 복잡도가 증가하기 때문에 center frequency가 같아야한다는 암묵적인 동의가 존재한다.

![Figure2](/assets/img/blog/RedCap/Figure2.png)

요약하면 RedCap UE의 flexibility를 위해 UL과 DL의 초기 BWP를 분리할 수 있지만, center frequency가 다른 경우 이를 해결하기 위해 적절한 retuning 절차를 사용해야 한다. 

Rel-17에서 논의 끝에 `Method 3`이 UE bandwidth mismatch를 해결하기 위한 방법으로 선정되었다.

### B. Collison Resolutions in Half-duplex Mode

HD-FDD는 크게 두 가지 모드, Type A와 Type B로 나눌 수 있다. 두 모드의 차이점은 UL과 DL에서 같은 local oscillator를 사용하는지 여부이다.

- HD-FDD Type A
  - UL과 DL이 서로 다른 local oscillator를 사용함
  - UL과 DL의 switching이 빠름

- HD-FDD Type B
  - UL과 DL이 같은 local oscillator를 사용함
  - 비용을 절감할 수 있지만 complexity 증가


Rel-17 RedCap UE는 HD-FDD Type A를 지원하기로 합의함
{:.note}

HD-FDD RedCap UE는 신호 송수신이 동시에 불가능하기 때문에 UL 송신과 DL 수신에 충돌이 발생할 수 있다.


이를 해결하기 위해
1. gNB에 의핸 합리적인 scheduling 필요
2. 표준에 최소한의 영향을 주는 송신 prioritiy들 정의

가 필수적이다.

충돌의 대표적인 예를 아래와 같이 정리할 수 있다.

- *Case 1: Collision due to direction switching*
  - UL과 DL이 HD-FDD Type A에서 direction switching을 하기 때문에 UL과 DL의 overlapping을 방지하기 위해 specific transition time이 필요하다.

- *Case 2: Dynamically scheduled DL reception conflicts with semi-statically configured UL transmission*
  - `partialCancellation`: UL symbol이 DL dynamically scheduled symbol과 overlap 되어 UL symbol의 전송이 취소될 수 있다.

- *Case 3: Semi-statically configured DL reception conflicts with dynamically scheduled UL transmission*
  - 동적으로 스케쥴링된 UL 송신이 semi-statically 하게 구성된 DL 수신보다 더 높은 우선순위를 가진다.

- *Case 4: Configured SSB conflicts with UL transmission*
  - Initial access 시 SSB가 매우 중요하기 때문에, UL 송신 시 configured SSB를 전송하는 것에 높은 우선순위를 둔다.
  
- *Case 5: Valid RO(RACH Occasion) conflicts with DL reception*
  - Valid RACH Occasio에 RedCap은 두 가지 선택을 할 수 있음.
    1. Downlink symbol을 계속 수신
    2. PRACH 자원을 사용해서 데이터를 전송
  - 이는 각각의 장비나 상황에 따라 결정됨

### C. Early Indication
RedCap UE의 early indication은 normal UE와 공존할 때 resource allocation에서 유용함. RedCap UE가 initial access 시 early identified 되면 적절한 scheduling이 시스템 성능을 개선하기 위해 이뤄질 수 있음.

Early indication에 두가지 옵션이 존재함

- **Early indication via Msg1**
  - RedCap UE에 대한 초기 신호를 더 빨리 제공하기 위해, PRACH(Physical Random Access Channel) 자원을 별도로 설정하거나, PRACH preamble을 분할하는 방식을 적용할 수 있다.
  - 이 방법은 강한 scalability와 simplicity를 가지고 있지만 PRACH resource overhead와 PRACH user capacity를 감소시키는 단점을 가지고 있다.
  
- **Early indication via Msg3**
  - Msg1 기반 early indication의 supplement로 사용
  - 이 방법은 네트워크의 커버리지 회복, Msg4의 link adaptation, 필요한 경우 Msgㅇ의 resource scheduling 등에서 더 나은 성능을 달성할 수 있게 도와준다.
  - Msg3에서 사용할 수 있는 여분의 비트 수는 매우 제한적이며 이를 사용하게 되면 네트워크와 터미널 장치의 complexity가 증가한다.
    - 이를 해결하기 위해 3GPP에서는 LCID(Logical Channel Index)를 사용하여 Msg3 기반 early indication을 적용하기로 합의하였다.
  
  ### D. Energy Saving
- DRX enhancement
  - `idle`과 `inactive` 상태에 있는 DRX cycle을 늘리는 것으로 고려중임.
- RRM measurement relaxation
  - RRM measurement 주기 늘리기, 측정 지표의 수 줄이기, 새로운 측정 기준을 도입하기 등
  - 최소한의 RRM measurement를 사용하여 장치의 배터리 수명을 연장시킨다.

## Analysis and Solutions for Coexistence Problems

 서로 다른 UE capability 때문에 early indication과 BWP configuration에서 coexistence 문제들이 발생할 수 있다. Early indication에서 특정 RedCap UE를 구분하는 문제가 있다.

 또한, Msg1과 Msg3을 이용한 합리적인 resource allocation이 추가적으로 논의되어야 한다.

 ![Figure3](/assets/img/blog/RedCap/Figure3.png)
 
 일반 UE와 다양한 타입의 RedCap UE가 공존하는 상황에서 하나의 initial BWP로는 성능 저하가 발생할 수 있다. 이를 개선하기 위해 여러 개의 초기 BWP들을 지원하는 것이 필요하다.

 1. Dedicated ROs
     - 특정 UE나 서비스에 전용으로 할당된 Random Access 기회를 의미함
     - 특정한 요구사항이 있는 통신(ex. 긴급 통신, 특수 장비 사용 등)을 위해 사용
     - Dedicated RO를 사용하면 해당 UE는 더 빠르고 확실한 네트워크 접속을 보장받을 수 있음

2. Shared ROs
   - 여러 UE가 공유하는 Random Access 기회를 의미함
   - ROs는 네트워크 자원을 여러 사용자가 공유하여 효율적으로 사용할 수 있게 한다.
   - Shared ROs는 네트워크 트래픽이 많은 시간대에 여러 사용자가 접속을 시도할 때 사용된다.

## Rel-18 RedCap UE Evolution
Rel-18 RedCap UE는 두개의 main 목표를 가지고 있다.
1. UE complexity와 비용 감소
2. DRX 메커니즘 강화

위와 같은 문제들은 Rel-17 RedCap, Rel-18 RedCap, Normal UE가 공존하는 상황을 바탕으로 연구가 된다.

# 3. Take Away
- RedCap에 대한 지식
- RedCap UE에서 추가로 연구될만한 분야(conflicts in resource allocation)
