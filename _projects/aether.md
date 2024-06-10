---
layout: project
title: 'Aether 5G'
caption: ONOS Project
description: >
  SD-RAN Enginnering Team 
date: 26 April 2024
image: 
  path: /assets/img/projects/aether/aether.png
  srcset: 
    1920w: /assets/img/projects/aether/aether.png
    960w:  /assets/img/projects/aether/aether@0,5x.png
    480w:  /assets/img/projects/aether/aether@0,25x.png
links:
  - title: Link
    url: https://docs.aetherproject.org/master/intro.html
sitemap: false
---

---


# Golang 프로젝트 분석 
  
## [Standard Go Project Layout](https://github.com/golang-standards/project-layout/blob/master/README.md)

<details>
<summary>Go 디렉터리</summary>
<div markdown="1">

### Go 디렉터리

- `/cmd`
  - 프로젝트의 메인 애플리케이션들
  
  각 애플리케이션의 디렉터리명은 만들고 싶은 실행 파일 이름과 같아야 함(e.g., /cmd/myapp)
  {:.note}

  - 애플리케이션 디렉터리에 많은 코드를 넣으면 안됨
    - `/pkg`: 다른 프로젝트들에서 import되고 사용될 것 같을 때 이용
    - `/internal`: 코드가 재사용성이 없거나 다른 사람들이 재사용하지 않기를 바랄 때 이용

- `/internal`
  - 개인적인 애플리케이션과 라이브러리 코드, 다른 사람들이 애플리케이션이나 라이브러리에서 임포트 하기를 원하지 않는 코드들
  - 이 레이아웃 패턴은 Go 컴파일러 자체에 강제됨

- `/pkg`
  - 외부 애플리케이션에서 사용되어도 괜찮은 라이브러리 코드
  - `/pkg` 디렉터리는 안의 코드가 다른 사람들에 의해 사용되어도 안전하다고 명시적으로 보여주는 좋은 방법임

- `/vendor`
  - 애플리케이션 의존성 관리는 수동 혹은 go modules 같은 의존성 관리 도구를 사용할 수 있음
  - go mod vendor 명령어는 `/vendor` 디렉토리를 생성해 줌

</div>
</details>

<details>
<summary>서비스 애플리케이션 디렉터리</summary>
<div markdown="1">

### 서비스 애플리케이션 디렉터리

- `/api`
  - OpenAPI/Swagger 스펙들, Json schema 파일들, 프로토콜 정의 파일들


</div>
</details>


<details>
<summary>웹 애플리케이션 디렉터리</summary>
<div markdown="1">

### 웹 애플리케이션 디렉터리

- `/web`
  - 웹 애플리케이션의 특정한 컴포넌트들: 정적 웹 에셋들, 서버 사이드 탬플릿과 SPA들


</div>
</details>


<details>
<summary>공통 애플리케이션 디렉터리</summary>
<div markdown="1">

### 공동 애플리케이션 디렉터리

- `/configs`
  - 설정 파일 템플릿이나 기본 설정들
  - `confd` 혹은 `consul-template` 템플릿 파일들

- `/init`
  - 시스템 init (systemd, upstart, sysv)과 프로세스 매니저/슈퍼바이저 (runit, supervisord) 설정

- `/scripts`
  - 빌드, 설치, 분석, 기타 작업을 위한 스크립트들
    - 스크립트들을 통해 Makefile을 작고 간단하게 유지할 수 있음
  
- `/build`
  - 패키징과 Continuous Integration(CI)
  - 클라우드(AMI), 컨테이너(Docker), OS(deb, rpm, pkg) 패키지 설정과 스크립트를 `/build/package` 디렉터리에 저장
  - CI(travis, circle, drone) 설정과 스크립트를 `/build/ci`에 저장

- `/deployments`
  - IaaS, PaaS, 시스템과 컨테이너 오케스트레이션 배포 설정과 템플릿 (docker-compose, kubernetes/helm, mesos, terraform, bosh).
  - 몇몇 레포지토리 (특히 쿠버네티스로 배포되는 앱들)에서 이 디렉터리는 `deploy`라고 불림

- `/test`
  - 추가적인 외부 테스트 앱들과 테스트 데이터들


</div>
</details>

<details>
<summary>다른 디렉터리들</summary>
<div markdown="1">

### 다른 디렉터리들

- `/docs`
  - 디자인과 사용자 문서들(godoc이 만든 문서도 포함)

- `/tools`
  - 이 프로젝트에서 사용하는 도구들
  - 이 도구들이 `/pkg`나 `/internal` 디렉터리에서 코드를 임포트할 수도 있음
  
- `/examples`
  - 애플리케이션 혹은 공개된 라이브러리의 예시들

- `/third_party`
  - 사용하는 외부 도구들, 포크된 코드들과 다른 서드 파티 유틸리티들(e.g., Swagger UI)

- `/githoooks`
  - Git hooks

- `/assets`
  - 레포지토리와 함께 사용될 asset들 (이미지, 로고 기타)

- `/website`
  - Github 페이지를 사용하고 있지 않을 때 프로젝트의 웹사이트 데이터를 넣는 곳
</div>
</details>


<details>
<summary>있으면 안되는 디렉터리들</summary>
<div markdown="1">

### 있으면 안되는 디렉터리들

- `/src`
  - 주로 Java 개발자들이 패턴에 익숙해져 생성
  - $GOPATH가 /src 디렉토리를 사용하고 있기 때문에, `/src` 디렉토리를 생성하지 않음
</div>
</details>