---
layout: post
title: Toward Smaller and Lower-Cost 5G Devices with Longer Battery Life - An Overview of 3GPP Release 17 RedCap 
tags: [ieee]
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
- 3GPP Release 17에 정의되어 있는 RedCap 심층조사
- RedCap을 포함한 RAN Simulator 개발에 필요

---


# 2. Summary of paper

## Introduction
- RedCap use cases는 wearables, industrial wireless sensors, 그리고 video surveillance를 포함한다.
- 이러한 use case들을 지원하기 위해, eMBB, URLLC, 그리고 mMTC 사이에 있는 use case들을 지원하기 위해 3GPP는 RedCap NR Device들을 정의하였다.
- 본 논문은 3GPP 표준 시점에서의 RedCap에 대한 overview를 제공한다.

## Use Cases and Requirements

|                             | Wearables                         | Industrial wireless sensors | Video surveillance                                    |
|-----------------------------|-----------------------------------|-----------------------------|-------------------------------------------------------|
| **Data rate** (Reference bit rate) | 5-50 Mbps in DL and 2-5 Mbps in UL* | 2 Mbps                      | 2-4 Mbps for economic video<br>7.5-25 Mbps for high-end video |
| **Latency**                 | -                                 | 100 ms                      | 500 ms                                                |
| **Availability/reliability**| -                                 | 99.99%                      | 99%-99.9%                                             |
| **Device battery lifetime** | At least few days and up to 1-2 weeks | At least few years          | -                                                     |
| **Traffic pattern**         | -                                 | UL heavy                    | UL heavy                                              |
| **Stationarity**            | Non-stationary                    | Stationary                  | Stationary                                            |

Peak bit rate for wearables can be up to 150 Mbps in DL and 50 Mbps in UL.
{:.note}

명확하게 말하자면, data rate와 latency는 NR eMBB/URLLC 만큼 높지 않고, 요구 배터리 수명은 mMTC만큼 길지 않다.

위의 세 가지 usecase들을 하나의 RedCap에서 지원하는 것이 바람직하다. 

![Figure1](/assets/img/blog/RedCap2/Figure1.png)

RedCap은 TR 38.875에서 정의되어있다.

## Reduced Device Capabilities
Release 17 RedCa work item으로 다음과 같은 항목들이 정의되었다.
- Reduction of maximum device bandwidth
- Reduction of minimum antenna configuration at the device
- Reduction of minimum supported number of DL MIMO layers
- Relaxed maximum DL modulation order
- Support for half-duplex operation in FDD bands

|                         | FR1 bands                                | FR2 bands                                |
|-------------------------|-------------------------------------------|------------------------------------------|
|                         | Reference device | RedCap device         | Reference device | RedCap device          |
| Maximum device bandwidth| 100 MHz          | 20 MHz                | 200 MHz          | 100 MHz                |
| Minimum antenna configuration¹ | 2 or 4 receiver branches | 2 receiver branches | 2 antenna panels, each supporting 4 dual polarized antenna elements | 2 antenna panels, each supporting 4 dual polarized antenna elements |
| Minimum supported number of DL MIMO layers | 2 or 4           | 1                     | 2                    | 1                   |
| Maximum DL modulation order | 256QAM         | 64QAM                 | 64QAM              | 64QAM                 |
| Duplex operation        | TDD or full-duplex FDD | TDD or half-duplex FDD | TDD                | TDD                   |
| Cost reduction          | 0%               | ~65%                  | 0%                 | ~50%                  |

¹ In 3GPP the requirements on physical antenna implementation at the device are not specified for FR1 and FR2. However, the effective isotropic radiated power, the effective isotropic receiver sensitivity, and the spherical coverage requirements specified for different power classes in FR2 implicitly impose requirements on the actual antenna implementation. The minimum antenna configuration for the reference device indicated in this table assumes power class 3 [6]. Both NR devices and RedCap devices may support a different power class with different set of requirements. The reference device and RedCap device support the same minimum number of receiver branches in FR2.

위의 표에 나열되어 있는 기능을 만족하면 상당한 비용 절감 효과를 얻을 수 있으며, 동시에 성능, 사양 및 공존(기기 간의 상호 간섭 등)에 미치는 영향은 관리 가능하다.

하지만 RedCap UE는 
1. FR1/FR2에서 20/100MHz 보다 넓은 주파수 대역
2. Carrier aggregation
3. Dual connectivity
4. 2 device receiver/transmitter branches
5. 2 DL/UL MIMO 레이어들

을 사용할 수 없다.

또한, Higher 레이어들에서의 제약사항들은 다음과 같다.
1. Data Radio Bearer(DRB) 수 감소: 기기가 필수적으로 지원해야 하는 DRB의 최대 수를 16개에서 8개로 감소
2. Sequence Number(SN) 길이 감소: PDU에서 PDCP 및 RLC 계층의 acknowledged 모드에 대한 SN 길이를 18비트에서 12비트로 감소
3. Automatic Neighbor Relation(ANR) 기능 비활성화: SON의 기능인 ANR 기능 지원이 필수 사항에서 제외

## Battery Lifetime Enhancement
아래의 기술들은 RedCap 기기에서 배터리 수명을 늘리기 위해 사용하는 기법들이다.

### A. Extended DRX for RRC idle and inactive states
Discontinuous reception(DRX) mechanism: NR 릴리즈 15에서 소개되었고 디바이스가 inactive 기간에서 `sleep` 모드로 설정된다.
{:.note}

일반적인 NR에서 DRX cycle의 최댓값은 2.56초이다. 하지만 RedCap use case에서는 eMBB/URLLC 만큼의 tight 혹은 deterministic한 latency 요구사항이 필요하지 않다.

따라서 extended DRX(eDRX)에서는 10485.76초까지 RRC `idle state` 그리고 `inactive state`는 10.24초까지 정의되었다.

![Figure2](/assets/img/blog/RedCap2/Figure2.png)
- IAT: Inter-Arrival Times


위의 그림은 다른 eDRX를 가지고 있을 때 battery 수명 증가를 그래프로 그린 것이다.

### B. RRM relaxation for stationary devices
`idle`과 `inactive`일 때 RSRP와 RSRQ에 기반한 RRM measurement는 serving cell과 neighbor cell들로부터 측정된다. 이러한 measurement들은 최적의 신호 quality를 달성하는데 도움은 되지만 데이터를 전송하지 않을 때 battery를 많이 소모하게 된다. 

Rel 16에서 low-mobility, not-at-cell-edge 시나리오에 대한 neighbor cell RRM measurements relaxation이 소개되었다.

Rel 17에서는 네트워크가 RRC 연결 상태의 장치에 대해 RSRP/RSRQ 기반의 정지 조건을 추가로 구성할 수 있다. 디바이스는 네트워크에 이 조건이 만족할 때와 만족하지 않을 때를 보고해야 한다.

## Coexistence, coverage, and capacity impacts

### A. Coexistence with other NR devices
RedCap과 다른 NR 디바이스간의 coexistence는 RedCap work item의 중요한 목표이다.
Coexistence에 다음 두 가지 challenge들이 존재한다.

1. Identification of RedCap devices
   - NR 장치가 RedCap인지 여부는 Random Access 절차 완료 후 네트워크에 알려지므로, 초기에 모든 장치가 가장 최소한의 기능을 가진 것으로 간주하고 스케쥴링 해야 한다.
   - RedCap 장치는 기능이 축소된 장치이므로, 네트워크가 이 장치를 기준으로 스케쥴링하면 일반 NR 장치도 그에 맞춰 제한적으로 동작하게 된다.
   - 이로 인해 네트워크 자원이 비효율적으로 사용된다.
   - 해결책: 네트워크가 Random Access 절차 중 장치가 RedCap 장치인지 아닌지를 미리 알 수 있게하는 방법이 도입됨.
     - Message 1: RedCap 전용 PRACH 리소스가 셀에 구성된 경우
     - Message 3: Random access 절차에서 Common Control Channel(CCCH)에 RedCap 전용 논리 채널 ID 값을 포함
  
2. Avoidance of PUSCH resource fragmentation
![Figure3](/assets/img/blog/RedCap2/Figure3.png)

 일반 NR 장치에 대해 구성된 initial BWP는 너무 넓을 수 있어 Release 17에서는 별도의 RedCap 전용 초기 UL 및 DL BWP 구성을 지원한다. 그러나 RedCap 전용 UL과 DL의 BWP가 carrier center frequency 근처에 배치되면, RedCap 장치의 POUCCH 전송이 다른 장치의 PUSCH 자원 단편화를 유발할 수 있다.
 
 이를 해결하기 위해 Release 17에서 RedCap 장치의 별도의 초기 UL BWP에서 PUCCH 주파수 호핑 비활성화를 지원하며, SSB 및 CORESET #0 없이 별도의 초기 DL BWP를 지원한다.

BWP configuration 주요 사항

- RedCap configuration 결정 요소
  - Carrier bandwidth
  - 최대 데이터 속도 요구사항
  - 장치 능력
  - 트래픽 활동

이 요소들을 고려하여 네트워크는 RedCap 장치에 적합한 UL/DL BWP 위치를 결정할 수 있다.

- 신호 오버헤드 관점
  - RedCap과 일반 NR 장치 간에 BWP 구성을 공유하는 것이 바람직하다.
    - 두 종류 장치 모두 동일한 BWP 설정을 사용하게 되어, 별도의 BWP 설정을 추가할 필요가 없다.
    - DL BWP 공유
      - RedCap 장치와 일반 NR 장치가 공유하는 DL BWP를 사용하면, 추가적인 BWP 설정이나 추가 SSB를 전송할 필요가 없다.
        - SSB는 기지국과 장치 간의 초기 통신을 설정하는데 사용되는 신호로, 추가 SSB 전송이 필요 없으면 네트워크의 신호량이 줄어든다.

위에서 설명한 개선 사항을 제외하고도 CSP가 SIB1에서 indication을 통해 RedCap 장치가 cell carrier에 접근하지 못할 수 있는 기능을 표준에서 정의하였다. 이는 네트워크 과부화 상황에서 셀 내 다른 NR 장치에 사용할 수 있는 무선 자원을 우선적으로 제공하거나, RedCap 장치의 접근이 다른 장치의 성능에 영향을 미칠 수 있다 의심되는 경우에 유용하다.

### B. Coverage impacts

Reduced capabilities는 디바이스의 커버리지에 영향을 미칠 수 있다. 안테나 사이즈와 대역폭의 감소가 coverage에 가장 큰 영향을 미치게 된다. Coverage에 미치는 영향을 평가하는 것도 study item 중 하나이다. 평가는 link budget analysis를 통해 이루어진다.

Message 1 (PRACH), Message 2 (PDSCH), Message 3 (PUSCH), and Message 4 (PDSCH), PUCCH, and the PDCCH scheduling Message 2/4 on common search space 모두 link budget analysis에 포함된다. 또한 analysis는 시골이나 도심지역에서 FR1, FR2를 가정한 다양한 환경에서 진행된다.

Link budget analysis에서 사용되는 metric
- Maximum Isotropic Loss (MIL): 기지국과 디바이스에서의 path loss와 beamforming gain을 포착

만약 reference device의 lowest MIL보다 RedCap 디바이스의 MIL이 낮다면 coverage recovery가 상응하는 channel에 필요하다.이 때, Reference device의 lowerst MIL과 RedCap 디바이스의 MIL 차이만큼 coverage recovery가 필요하다.

Link budget analysis 결과는 다음과 같이 요악할 수 있다.
- UL(FR1 & FR2)
  - FR1과 FR2의 UL에서 coverage recovery 필요없음
- DL (FR1)  
  - Urban micro 시나리오
    - RedCap 장치가 1개의 수신기 branch(antenna)를 가진 경우 Message 2에 대해 커버리지 회복 필요
    - 2개의 수신기 브랜치를 가진 경우 커버리지 회복이 필요하지 않음
- DL (FR2)
  - Coverage 회복 필요 여부는 RedCap 및 참조 장치의 maximum transmitted radiated power(TRP) 선택에 따라 다름
  - TRP 23 dBm 가정 시:
    - Message 2, Message 4, PDSCH와 같은 일부 DL 채널에 대해 커버리지 회복 필요
  - TRP 12 dBM 가정 시:
    - DL 채널에 대해 커버리지 회복 필요하지 않음

이 때 커버리지 회복에 대해 쓰이는 기술은 Rel 15/16에서 쓰이는 기술들을 최대한 활용한다.

### Capacity imapcts
RedCap 기기의 lower capability는 시스템 성능에 악영향을 준다. 이를 평가하기 위해 3GPP에서 SLS를 수행하였다.
![Figure4](/assets/img/blog/RedCap2/Figure4.png)

Figure 4는 FR1에서 1개의 수신기 branch를 가진 RedCap 장치의 비율이 증가하는 경우(0% - 90%)에 따른 셀 load에 대한 eMBB 사용자들의 DL 사용자 처리량을 보여준다.

그림에서 볼 수 있듯이 채널 상태가 좋은 eMBB 유저들은 RedCap 유저들이 증가해도 크게 영향을 받지 않는다. 중간에 있는 유저들은 RedCap 유저들이 증가할수록 throughput이 감소하는 것을 확인할 수 있다. 이는 RedCap 장치가 스펙트럼 효율이 낮아 주어진 load에서 더 많은 자원을 사용하기 때문이다.

- 트래픽 모델링
  - eMBB users: $$2 * 10^7 $$ bit/s (200ms 마다 0.5MB 전송)
  - RedCap users: $$4 * 10^5$$ bit.s (2초마다 0.1MB 전송)
  - RedCap user의 부하는 eMBB 사용자의 부하보다 50배 낮음

## Conclusion
본 논문은 3GPP 표준에서 어떻게 RedCap을 디자인하는지에 대한 overview를 제공하고 있다. 또한 RedCap과 다른 NR 디바이스들의 coexistence를 어떻게 표준이 실현시켰는지 보여주고 있다. 또한 Release 18의 RedCap도 Release 17 기반으로 강화될 것으로 예상한다. RedCap의 발전으로 Release 17 usecase에 대해 향상된 지원을 제공할 뿐 아니라, smart grid와 같은 새로운 usecase 세그먼트로의 확장을 지원할 수 있다.

# 3. Take Away
- Simulator 구성 시 필요한 metric
- RedCap이 프로토콜이 아닌 device의 한 종류란 것