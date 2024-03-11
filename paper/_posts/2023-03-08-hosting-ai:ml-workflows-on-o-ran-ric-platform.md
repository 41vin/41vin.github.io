---
layout: post
title: Hosting AI/ML Workflows on O-RAN RIC Platform
description: >
  Samsung Research에서 나온 논문을 정리한 글입니다.
image: /assets/img/blog/o-ran.png
sitemap: false
---


본 글은 O-RAN SC Community의 공식문서를 참고하여 작성하였습니다.
{:.note}

본 포스트는 O-RAN SC의 Near RT RIC(RAN Intelligent Controller) Release `I`를 설치하는 과정을 처음부터 정리한 것입니다.

Near-RT RIC을 구축하는 방법에는 크게 3가지 방법이 있습니다.
1. 'Traditional' install shell script을 이용하는 방법: 기존 공식 Documents에서 기술되어 있습니다.
2. Using a kubernetes operator
3. Using an "umbrella" helm chart

2번과 3번 방법은 `I` release에서부터 추가된 방법입니다.
---

본 포스트에서는 1번 방법을 이용해 Near-RT RIC 환경을 구축해보겠습니다.

O-RAN SC Near-RT RIC 환경은 아래와 같습니다.
- Kubernetes: v1.16.0
- CNI: v.0.7.5
- Helm: v.3.5.4
- Docker: v20.10.21

## 1. Docker 수동 설치
### 1. Docker Github Release 페이지 방문
Docker의 버전이 너무 오래되어 apt 저장소에서 누락되었기 때문에 [Github 페이지](https://github.com/moby/moby/releases/tag/v20.10.21)를 방문하여 수동으로 바이너리 파일들을 다운 받았습니다.

### 2. docker-20.10.21.tgz 파일 압축 풀기
터미널에서 다운로드한 파일이 있는 디렉토리로 이동한 후, 다음 명령어를 실행합니다.
``` bash
tar -xvzf docker-20.10.21.tgz
```


### 3. 바이너리 파일 이동
압축 해제 후 docker 폴더가 생성됩니다. 이 파일안에 있는 바이너리 파일들을 시스템의 PATH에 있는 디렉토리로 이동해야 합니다. 제 디렉토리는 '/usr/local/bin'이었습니다. 아래의 명령어를 입력하여 압축 해제된 바이너리 파일들을 'usr/local/bin'으로 이동시킵니다.
~~~ bash
sudo mv docker/* /usr/local/bin/
~~~

### 4. Docker 데몬 시작
Docker 바이너리를 설치한 후, docker 데몬을 시작해야 합니다. 아래의 명령어를 입력하여 docker 데몬을 시작합니다.
``` bash
sudo dockerd &
```

### 5. Docker 작동 확인
Docker가 정상적으로 설치되고 실행중인지 확인합니다. 전 아래의 입력을 확인하여 docker의 버전을 확인하였습니다.
``` bash
docker --version
```

아래의 그림은 docker v20.10.21을 성공적으로 설치한 것을 캡쳐한 것입니다.


## 2. Kubernetes 수동 설치
Docker와 마찬가지로 Kubernetes 버전이 너무 오래되었기 때문에 apt 저장소에서 더 이상 지원을 하지 않고 있는 상황입니다.

이와 관련된 issue를 현재 [zira](https://jira.o-ran-sc.org/browse/RIC-1049)에 올린 상황입니다. 새로운 kubernetes 버전 지원이 필요하다고 올렸지만 issue를 읽어줄지 사실 잘 모르겠습니다.

수동으로 kubernetes v1.16.0을 설치하는 과정을 정리해보았습니다.

### 1. 필요한 바이너리 다운로드
Kubernetes의 [Github release 페이지](https://github.com/kubernetes/kubernetes/releases)에서 v1.16.0의 kubectl, kubelet, kubeadm 바이너리를 다운 받습니다.

아래의 명령어를 입력하여 바이너리를 다운받습니다.(Linux amd64 기준으로 명령어를 입력하였습니다.)

~~~ bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/linux/amd64/kubectl
wget https://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/linux/amd64/kubelet
wget https://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/linux/amd64/kubeadm
~~~

### 2. 바이너리 실행 권한 부여 및 이동
다운로드한 파일에 실행 권한을 부여하고, 'usr/local/bin'의 디렉토리로 이동시킵니다.
``` bash
chmod +x kubectl kubelet kubeadm
sudo mv kubectl kubelet kubeadm /usr/local/bin/
```

### 3. kubelet과 kubeadm으로 kubernetes 클러스터 초기화
'kubeadm'을 사용하여 클러스터를 초기화합니다.
``` bash
sudo kubeadm init --kubernetes-version=v1.16.0
```

d