---

---



# MDT(Minimalization of Drive-Tests) 분석 _ Release 9 TR 36.805 

## 1. 목적 및 Scope

- 표준에서 LTE/HSPA 네트워크의 MDT 사례와 요구사항을 정의
  - HSPA: High Speed Packet Access, 고속 패킷 기반 데이터 전송 기술
- 자동화된 UE 측정을 이용해 drive-test 필요성을 최소화하는 것을 목표로 함
- 기존 UE measurement 기능을 활용한 logging 및 reporting 변화에 중점을 두고 있음



 ## 2. 요구사항 및 제약조건

- UE measurements는 network configurations와 독립적으로 설정해야 함
  - 네트워크마다 기술의 버전이 다르기 때문에 UE 측정이 네트워크 설정에 의존하면 모든 네트워크에서 작동하지 않을 수 있음. 따라서 UE 측정이 네트워크 설정과 상관없이 독립적으로 이루어져야 모든 네트워크 환경에서 잘 동작할 수 있음
- Measurement logs는 시간에 따라 수집 및 보고되어야 하며, UE battery consumption 및 network signaling 부하를 최소화해야 함
- SON과 독립적으로 작동해야 함
  - MDT가 SON에 의존하면, MDT의 기능이 SON이 제공하는 데이터 알고리즘, 관리 도구와 밀접하게 연결이 되게 됨. 따라서 네트워크 특정 영역에서 데이터를 수집하고 분석하는 MDT의 기능은 SON 시스템이 제공하는 자동화된 네트워크 최적화 기능에 의존하게 됨.
  - SON이 없는 네트워크 환경에서 MDT를 효과적으로 사용할 수 없기 때문에, SON 기술에 의존하지 않고 독립적으로 발전이 되어야 함



## 3. Use Cases

- 커버리지 최적화, 모빌리티 최적화, 용량 최적화 등의 다양한 네트워크 시나리오들을 포함하고 있음
- 새로운 셀의 배치, 주요 인프라 구축(빌딩, 다리 등), 고객 불만 대응 및 주기적인 drive tests 등의 상황에서 적용됨



## 4. UE Measurements

- 기존의 RRC signaling principles을 활용해 필요한 정보를 수집
- 새로운 기능의 필요는 최소화하고, 기존에 간으한 measurement functionalities를 최대한 활용



## 5. Measurement Logging Types

- 다양한 유형의 logging이 상세히 설명됨: 주기적 다운링크 파일럿 측정, 서빙 셀의 임계값 이하 측정 등.
- 각 로깅 유형의 benefits, triggers, configuration parameters에 대한 정보 제공됨.
- 예를 들어, 주기적 다운링크 파일럿 측정은 일정 시간 간격(예: 2초)으로 DL 측정치를 logging하며, 이는 커버리지 데이터의 정확도를 30% 이상 향상시킬 수 있음.



## 6. Impact Analysis

- UE power consumption 및 memory에 미치는 영향 분석 포함.
- 추가 기능에 대한 필요성과 location information 요구사항, memory requirements에 대한 논의 진행.
- 예를 들어, 주기적 로깅이 활성화되면 UE의 power consumption이 평균 10-15% 증가할 수 있으나, 이는 network maintenance cost의 20% 감소로 상쇄될 수 있음.



## 7. Conclusion

- MDT implementation은 network operators에게 많은 혜택을 제공함. 드라이브 테스트 필요성 감소 및 network maintenance cost 절감에 도움이 될 것으로 예측

  