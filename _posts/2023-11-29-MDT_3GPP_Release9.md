---
category: 3GPP 
tags: [Rel9]
usemath: [latex, ascii] 
---

# MDT (Minimization of Drive Tests) 표준 조사 (Release 9)

## 요구 사항 및 제약 사항

드라이브 테스트 최소화를 위한 해결책은 다음 요구 사항을 준수한다.

1. **UE 측정 구성 (UE Measurement Configuration)**
2. **UE 측정 수집 및 보고 (UE Measurement Collection and Reporting)**
3. **측정 로깅의 지리적 범위 (Geographical Scope of Measurement Logging)**
4. **위치 정보 (Location Information)**
5. **시간 정보 (Time Information)**
6. **장치 유형 정보 (Device Type Information)**
7. **SON (Self-Organizing Networks) 의존성**

또한, 다음과 같은 제약 사항을 고려한다.

1. **UE 측정 (UE Measurements)**
2. **위치 정보 (Location Information)**

## 사용 사례

### 커버리지 최적화 (Coverage Optimization)

- 네트워크 커버리지 정보는 네트워크 계획, 최적화 및 RRM(Radio Resource Management) 파라미터 최적화에 필수적이다.
- **네트워크 제공 커버리지/처리량을 알아내기 위해 엄격한 “드라이브 테스트”가 수행된다.**
- 주요 트리거는 다음과 같다:
  1. **새 기지국/셀 배포**
  2. **새로운 고속도로/철도/주요 건물 건설**
  3. **고객 불만**
  4. **주기적 드라이브 테스트**

### 이동성 최적화 (Mobility Optimization)

- 이동성 문제나 실패 정보는 커버리지 부족 또는 네트워크 파라미터 설정의 필요성을 식별하는 데 사용된다.

### 용량 최적화 (Capacity Optimization)

- 새 셀의 배치, 공용 채널 구성 및 기타 용량 관련 네트워크 파라미터를 최적화하는 데 도움이 된다.

### 공용 채널의 파라미터화 (Parameterization for Common Channels)

- UL 또는 DL 공용 채널 커버리지 문제 탐지나 성능 분석은 네트워크 파라미터 설정 및 시스템 성능 최적화를 위해 도움이 된다.

### QoS 검증 (QoS Verification)

- 네트워크 성능 분석의 목적 중 하나는 서비스 품질 검증이다. 이는 중요한 조건을 탐지하고 네트워크 구성, 파라미터 설정 또는 용량 확장의 필요성을 결정하는 데 도움이 된다.

## UE 측정 (UE Measurements)

- 기본 모델/원칙
  - 네트워크는 UE에 일부 측정 로깅을 요청할 수 있으며, UE는 위치 정보 가용성과 같은 특정 제약 사항을 고려하여 요청된 로깅을 수행한다.
- 라디오 환경 측정 (Cell Measurements)
  - OAM 요구 사항에 기반하여 구성된 측정을 위해 적절한 측정이 RRM에 의해 제어된다.
- 위치 정보
  - UE의 위치 결정 구성 요소 사용을 지나치게 많이 사용하면 UE의 전력 소비가 크게 증가할 수 있다.

### UE 측정 로깅 (UE Measurement Logging)

#### 주기적 다운링크 파일럿 측정 (Periodical Downlink Pilot Measurements)

- **설명**: CPICH RSCP, CPICH Ec/No, TDD P-CCPCH RSCP 및 ISCP, RSRP 및 RSRQ(연결 모드 전용)와 같은 라디오 환경 측정이 주기적으로 기록된다.
- **이점**: 운영자는 DL 커버리지/달성 가능한 처리량을 파악하기 위해 DL 공용 파일럿 수신 레벨과 SIR 레벨을 주요 측정한다.

#### 기타 중요한 UE 측정 로깅

- **서빙 셀이 임계값보다 나빠짐 (Serving Cell Becomes Worse Than Threshold)**
- **전송 파워 헤드룸이 임계값보다 적음 (Transmit Power Headroom Becomes Less Than Threshold)**
- **랜덤 액세스 실패 (Random Access Failure)**
- **페이징 채널 실패 (Paging Channel Failure - PCCH Decode Error)**
- **방송 채널 실패 (Broadcast Channel Failure)**
- **라디오 링크 실패 보고 (Radio Link Failure Report)**

------

## 영향 분석 (Impact Analysis)

### UE 영향 (UE Impact)

#### UE 전력 소비 (UE Power Consumption)

- **MDT 측정의 활성 모드 중 가용성**: 추가 측정 수행 및 저장 필요
- **유휴 모드 및 DRX가 MDT 측정에 미치는 영향**: 유휴 모드 측정에 대한 상세 요구 사항은 명시되어 있지 않다
- **UE에서 위치 정보의 가용성 및 정확도**: MDT에 대한 위치 정보 요구 사항은 UE의 전력 소비에 영향을 미칠 수 있다

#### UE 메모리 영향 (UE Memory Impact)

- 독립적인 로깅 트리거와 보고 트리거는 UE가 네트워크에 보고될 때까지 측정을 저장해야 함을 시사한다.