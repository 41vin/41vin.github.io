---

---



# Possible Solutions for Allignment of TracjeJob and PerfMetricJob

## Key issue #1: Reporting of Collected Management Data

### Problem Statement

1. **PerfMetricJob**과 **TraceJob**의 reporting 방법 차이:
   - **PerfMetricJob**: fileReportingPeriod을 정의
   - **TraceJob**: 즉각적인 MDT 측정을 위한 여러 parameter 존재 (예: tjMDTReportInterval, tjMDTCollectionPeriodRrmNR 등). 이 parameter들은 주기적인 Mx 측정 사이의 간격을 정의하지만, gNB에서 TCE로 파일이 언제 전송되는지는 명시하지 않음.
2. 특정 작업 유형에서 **reporting period의 부재**:
   - "TRACE_ONLY", "RLF_REPORT_ONLY", "RCEF_REPORT_ONLY" 작업 유형의 경우 reporting period가 지정되지 않음.
   - "LOGGED_MDT_ONLY" job에서는 tjMDTLoggingInterval parameter가 downlink pilot 강도 측정 사이의 간격을 결정함.
3. **파일 저장 위치 선택**:
   - **PerfMetricJob**: MnS consumer 또는 producer가 파일이 저장될 위치를 선택할 수 있음. MnS producer가 위치를 선택하는 경우, 새 파일의 availability나 파일 준비 중 실패에 대해 MnS consumer에게 알림.
   - **TraceJob**: 파일이 저장될 위치는 MnS consumer와 producer에 의해 수정될 수 있음. 이는 "File data reporting service"에 정의되어 있지만, TraceJob IOC의 설명에서는 존재하지 않음.



### Potential Solution

TraceJob에 적용 가능한 알림을 강화함으로 문제를 해결할 수 있음. PerfMetricJob에 사용되던 notifyFileReady 및 notifyFilePreparationError 알림등을 TraceJob함으로 두 유형 간의 일관성 증가

---



# Possible solutions for TraceJob enhancements in the context of SBMA

- SBMA: Service Based Management Architecture

## Key issue #1: name containment in case of handover for signalling-based trace activation

### Problem Statement

1. **Name Containment in TraceJob:**
   - TraceJob은 SubNetwork, ManagedElement 혹은 ManagedFunction에 의해 이름이 지정됨. 하지만 Signal-based trace activation의 경우, 특정 UE에 대해 IMSI, IMEI(SV), IMEI-TAC 또는 SUPI를 지정하여 활성화함. UE가 다른 셀로 handover 될 때에도 management 데이터의 logging은 유지됨.
2. **Handover 중 trace 관리 방법이 정의되어 있지 않음**:
   - RAN에서 handover 도중 trace 관련 parameter가 연관되어 있는 gNB로 전파됨. 하지만 NRM(Network Resource Model)에서 signalling-based trace 활성화에 대한 handover 시 TraceJob MOI(Management Object Instance)의 처리 방식이 명시되어 있지 않음
     - MOI: NRM에서 관리되는 특정 리소스 또는 객체의 인스턴스

위의 문제들의 핵심은 handover가 발생할 때, TraceJob의 MOI가 어떻게 처리되어야 하는지 NRM에서 명시적으로 정의되어 있지 않다는 것임.



### Potential Solution

1. 사용자 신호 trace 활성화:
   - 네트워크 관리 시스템이 특정 UE의 신호를 trace하도록 5G 코어 네트워크에 지시함. 사용자를 식별하기 위해 IMSI, IMEI 등의 정보를 활용함. 이 지시에 따라 코어 네트워크의 **UDM(User Data Management)**은 네트워크 기지국(gNB)에 사용자의 신호를 trace하도록 설정함.
2. Handover 시 trace configuration 전달:
   - 사용자가 다른 셀로 이동하는 handover가 발생할 때, 원래의 gNB는 trace configuration을 다음 기지국으로 전달함. 이렇게 하면 다른 셀로 이동해도 signal trace가 계속될 수 있음.
3. UDM에서 name containement 관리:
   - Signaling based trace가 활성화 된 경우, 해당 TraceJob은 코어 네트워크의 UDM에 의해 관리되게 설정함. 이렇게 하면 사용자가 다른 셀로 이동하더라도 코어 네트워크에서 동일하게 traceJob을 관리할 수 있음.

결론적으로 handover가 발생해도 traceJob이 연속적으로 관리될 수 있도록 signaling based traceJob 활성화 시UDM에서 traceJob을 관리해주는 것이 중요함.

---



## Key issue #2: Gaps in Signalling Trace Activation

### Problem Statement

Signal trace가 활성화될 때, 네트워크에 연결되는 초기 단계의 메시지 교환 정보가 수집되는 trace 기록에 빠져 있음. 이는 trace activation 메시지가 코어 네트워크에서 기지국으로 도달하기 전에 해당 메시지 교환이 발생하기 때문임.

&rarr; Trace activation 명령이 기지국에 도달하고 실행되기 전에 네트워크 연결 과정에서 중요한 초기 메시지들이 이미 전송되고 처리됨.

![image-20231203192433010](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/MDT_rel18/image-20231203192433010.png)



### Potential Soltuion

- NG-RAN의 메시지 버퍼링
  - UE가 통신을 시작할 때, NG-RAN은 교환된 메시지를 버퍼에 저장함. Trace가 trigger되면, NG-RAN은 이 버퍼에 저장된 모든 메시지를 trace 기록에 추가.
  - 위의 그림에서 3-5 단계의 메시지가 NG-RAN에 버퍼됨.

이러한 해결 방안은 초기 네트워크 연결 단계의 중요 메시지를 trace 기록에 포함시켜 네트워크 문제 해결 및 디버깅을 개선하는데 도움이 됨.

---



## Key issue #3: Structuring of TraceJob IOC

### Problem Statement

TraceJob 인스턴스는 특정 traceJob 인스턴스에 대한 trace 제어 및 구성 parameter를 나타냄. 이러한 parameter는 trace message, immediate measurements, logged MDT measurements, logged MBSFN MDT measurements, RLF and RCEF reports를 보고하는데 사용됨. 30개 이상의 attribute가 있으며, 대부분의 parameter는 특정 조건에 의존하고 있음.

이 parameter들이 'jobType'이라는 attribute에 따라 다르게 설정되어야 함. 하지만 현재 TraceJob의 attribute들은 jobType에 따른 의존성을 잘 반영하고 있지 않음. 

&rarr; 어떤 'jobType'을 선택하느냐에 따라 필요한 parameter가 달라지는데, 이를 명확하게 구분하고 정리하는 구조가 부족함. 이로 인해 TraceJob을 설정할 때 어떤 매개변수를 사용해야 하는지 파악하기 어려움.



## Potential Solution

- TraceJob attribute의 구조화
  - 기존의 TraceJob의 속성들은 jobType 속성을 기준으로 구조화될 수 있음. 이러한 속성에는 plmnTarget, traceReportingConsumerUri, traceCollectionEntityIpAddress, traceReference, traceRecordingSessionReference, traceReportingFormat, traceTarget 등이 포함됨.
  - TraceJob IOC(Information Object Class) 아래에 TraceConfig 및 MDTConfig라는 새로운 데이터 타입을 도입하여 구조화할 수 있음.
    - TraceConfig 데이터 타입은 jobType Trace 및 Trace와 즉각적인 MDT에 특화된 속성을 포함함.
    - MDTConfig 데이터 타입은 즉각적인 및 로그된 MDT 유형에 공통적인 속성을 포함함.
  - 즉각적인 MDT 및 로그된 MDT에 특화된 attribute는 각각 ImmediateMdtConfig 및 LoggedMdtConfig라는 새로운 데이터 타입에 구조화 됨.

&rarr; TraceJob은 다양한 jobType에 맞게 재구조화될 수 있음. 이를 통해 trace job을 더 효율적으로 관리하고 소비자가 필요한 데이터를 쉽게 구성할 수 있음.

---

# Possible Solutions for MDT Enhancements

## Management of Data Collection Enhancements of MDT

### Key issue #1: Alignment of "Report Amount" parameter for Immediate MDT measurements

#### Problem Statement

 "Report Amount" parameter는 immediate MDT 측정에만 적용되며, 이는 주기적 report를 위한 measurement report의 수를 정의함. 그러나 이 parameter는 M1 measurement에만 적용되며, 다른 measurement type(M4, M5, M6, M7)에 대한 별도의 configuration이 부족한 상황임.



#### Potential Solution

1. Single configuration parameter Report Amount for M1, M4, M5, M6 and M7
   - 단일 "Report Amount" parameter를 사용하는 방안. 이는 설정의 간소화를 가져오지만, 다양한 측정 유형에 대해 다른 "Report Amount" 값을 설정할 수 없는 단점이 있음.
2. Individual configuration parameter Report Amount for M1, M4, M5, M6 and M7
   - 각각의 MDT 측정 유형(M1, M4, M5, M6, M7)에 대해 별도의 "Report Amount" parameter를 정의하는 방안. 이는 각 측정 유형에 대해 다른 "Report Amount" 값을 설정할 수 없는 단점이 있음. 하지만 관리 복잡성이 증가하고 오류 가능성이 증가한다는 단점이 존재함.

---



### Key issue #2: Reporting of per direction per DRB per UE MDT measurements

#### Problem Statement

 Trace 데이터 파일 XML에서 MDT 측정은 'meas'로 표현됨. 'meas'는 여러 MDT 측정을 나타낼 수 있으나 UL/DL이나 DRB(Data Radio Bearer)에 대한 정보를 포함하지 않아 immediate MDT 측정(M4, M5, M6, M7)을 UL/DL과 DRB별로 보고하는 데 필요한 수단이 부족함. 



#### Potential Solution

XML 스키마의 기존 'meas' 요소를 개선해 DRB와 UL/DL 정보를 포함시키는 방안 사용 가능함.

- UL/DL: Boolean 값으로 정의
- DRB: 해당 measurement의 DRB 번호를 나타내는 정수

&rarr; XML trace file에 새로운 속성을 추가하는 업데이트를 통해 해결

---

## Management of MDT for NPN

### Key issue #1: Support of management and signalling based traceMDT collection for NPN networks

- NPN: Non-public Network



 #### Problem Statement

NPN을 위한 traceMDT 수집을 지원할 때 다음과 같은 문제점 발생

1. NPN network에서 traceMDT 수집을 지원하기 위한 요구사항(비즈니스 및 사양 수준)이 존재하지 않음.
2. NPN network를 고유하게 식별하기 위한 방법이 충분치 않음.
3. Management-based activation 시 SNPN에서 traceMDT 수집을 지원하기 위해 PLMN ID와 일치시키는 기존 procedure가 불충분함.
4. NPN에서 management-based MDT에 UE를 선택하기 위한 기준으로 UE의 RPLMN ID만 고려하는 것은 충분치 않음.
5. PNI-NPN에서 closed access groups(CAG)에 의해 도입된 새로운 dimension을 포함하기 위한 기존의 area 정의가 불충분함.



#### Potential Solution

- NPN에 대한 traceMDT 수집을 지원하기 위해 trace management system의 요구 사항을 강화
- NID 및 CAG ID와 같은 적절한 식별자를 포함하여 기존 방법을 강화
- SNPN 및 PNI-NPN에 대한 관리 기반 활성화 시 PLMN ID와 함께 NID 및 CAG ID를 일치시키는 procedure 강화
- Management-based MDT activation procedure를 강화해 SNPN 및 PNI-NPN에 대한 식별자를 UE 선택 기준에 포함
- PNI-NPN에 대한 area 범위 정의를 CAG ID를 포함하도록 강화

---



### Key issue #2: Support of RLF and RCEF reports for NPN networks

#### Problem Statement

NPN에서 RLF(Radio Link Failure) 및 RCEF(Radio Cell Failure) Reports 지원을 위한 비즈니스 및 specification level requirements 부족. 기존 trace activation 및 deactivation 방법은 지원하지 않는 NPN이 존재하기 때문에 사용 어려움.



#### Potential Solution

Requirements 추가

- 비즈니스 및 specification level requirements를 표준에 추가해 NPN에서 RLF 및 RCEF Reports를 지원하도록 함

---



# Management of MDT for MRO

### Key issue #1: Support of MDT for MRO during inter-system inter- RAT handover for voice fallback

- MRO: Mobility Robustness Optimization

#### Problem Statement

Voice fallback(고속 데이터 네트워크에서 음성 통화를 지원하기 위한 기술, 음성 통화를 처리하기 위해 기존의 2G 또는 3G 네트워크로 잠시 'fallback'하게 됨)을 위한 inter-system, inter-RAT handover 시나리오에 대해. RAN3는 MRO 향상을 위한 두 가지 경우를 논의함.

1. **Case 1**: NR에서 E-UTRAN으로의 핸드오버 실패(Hand Over Failure / Radio Link Failure) 후, 적절한 E-UTRA 셀이 선택되고 UE는 해당 셀에서 음성 서비스를 위한 RRC 연결 설정 절차를 시도
2. **Case 2**: NR에서 E-UTRAN으로의 핸드오버 실패 후, 적절한 E-UTRA 셀을 찾지 못하면 UE는 원래 NR의 소스 PCell 구성으로 돌아가 NR에서 RRC 재설정 절차를 시작

또한 기존의 연결 실패 유형에 새로운 유형을 추가해야할 필요성 증가



#### Potential Solution

Case 2에 대한 solution

- Management-based MDT activation
  - gNB에서 UE가 RRC 연결을 시도하는 경우, MDT 데이터 수집은 정상적으로 가능
- Signaling based activation
  - AMF에서 gNB로 보내는 초기 context 설정 요청에는 UDM의 MDT 구성에 따른 MDT configuration이 포함됨. UDM은 MDT configuration을 AMF 및 SMF로 전달하며, AMF의 MDT configuration을 gNB로 전달함



