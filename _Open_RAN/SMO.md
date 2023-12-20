---

---



# Service Management and Orchestration (SMO)

## Introduction 

The SMO supports a number of interfaces

1. O1
2. O1/VES
3. O2
4. A1
5. R1



## O1 Interface

- NETCONF 프로토콜을 지원하여 O-RAN 솔루션의 네트워크 요소를 구성하고 관리함.
  - Near RT-RIC, O-CU, O-DU, O-RU가 포함됨.
- SMO는 데이터 모델을 사용하여 네트워크 요소의 구성 및 관리를 주도함.
- OpenDayLight(ODL)의 NETCONF 구현을 기반으로 하며, UI는 ODL Community GUI (DLUX)를 기반으로 함.



## Testing of Data Models

- **SMO와 OAM의 협력:** SMO는 OAM 프로젝트와 협력하여 O-RAN 솔루션을 위해 발표되는 데이터 모델을 테스트하고 주도함.
- **모델 출처:** 이 데이터 모델들은 3GPP 또는 O-RAN 자체에서 나올 수 있음.
- **모델 위치:** 데이터 모델들은 NETCONF 서버에 저장되며, Near RT RIC, O-CU-UP, O-CU-CP, O-DU, O-RU 등에서 찾을 수 있음.
- **NETCONF 클라이언트의 request:** NETCONF 세션이 설정될 때, NETCONF 클라이언트(예: SMO)가 이 모델들을 request함.

- **API 구동:** 그 후, Postman과 같은 application을 사용해 SMO가 노출하는 North-bound API를 구동할 수 있음.

![O1 Interface and SMO](https://wiki.o-ran-sc.org/download/thumbnails/20876079/o-ran-o1-interface-with-postman.png?version=1&modificationDate=1601411021413&api=v2)



# SMO and App Onboarding

## Introduction

- **SMO의 목적:** SMO의 주요 목적 중 하나는 애플리케이션을 온보드하는 것임. 여기에는 non-RT RIC에서 실행되는 rApps 또는 near-RT-RIC에서 실행되는 xApps가 포함됨.
- Application을 onboard하기 위해, SMO는 application이 어떻게 패키징되는지 이해할 필요가 있음.



## O1/VES Interface

- O1/VES 인터페이스는 SMO의 monitoring side를 지원함.

![image-20231204141811703](/Users/mcl/Library/Application Support/typora-user-images/image-20231204141811703.png)

- VES Agents:
  - 이벤트를 수집하고 VES 이벤트 형식으로 포맷
  - 수집된 이벤트를 VES Collector로 전송
- VES Collector:
  - 이벤트를 InfluxDB에 저장
  - 또는 ElasticSearch 엔진이나 Kafka 버스에 이벤트를 저장 가능
- 데이터 분석:
  - Grafana나 다른 애플리케이션을 통해 이벤트 데이터를 추출하여 분석 가능

![image-20231204142037069](/Users/mcl/Library/Application Support/typora-user-images/image-20231204142037069.png)