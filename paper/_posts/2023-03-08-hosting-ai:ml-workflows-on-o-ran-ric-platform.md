---
layout: post
title: Kubernetes 클러스터 구축하기
description: >
  On-premise 환경에서 클러스터를 구축하는 방법을 정리하였습니다
image: /assets/img/blog/Kubernetes_logo.png
sitemap: false
---


본 글은 Kubernetes 공식 문서를 참고하여 작성하였습니다.
{:.note}


본 포스트는 O-RAN SC의 Near RT RIC(RAN Intelligent Controller) Release `I`를 설치하는 과정을 처음부터 정리한 것입니다.

Near-RT RIC을 구축하는 방법에는 크게 3가지 방법이 있습니다.
1. 'Traditional' install shell script을 이용하는 방법: 기존 공식 Documents에서 기술되어 있습니다.
2. Using a kubernetes operator
3. Using an "umbrella" helm chart

2번과 3번 방법은 `I` release에서부터 추가된 방법입니다.

본 내용에 들어가기에 앞서 개발을 진행한 컴퓨터는 

- OS: Ubuntu 20.04 LTS
- Intel NUC 12

임을 미리 알려드립니다.

---

본 포스트에서는 수동으로 kubernetes 클러스터를 만든 후 Near-RT RIC 환경을 배포해보겠습니다.

기존 쿠버네티스 버전이 1.16.0으로 너무 오래되었기 때문에 virtualbox를 이용하여 v1.29.2의 kubernetes 클러스터를 구축해보았습니다.

Node의 구성은 
- OS: ubuntu 20.04
- Controlplane: 1 
- Worker: 2
  
로 설정하였습니다.

## 가상머신 구성

### 1. 링크를 클릭하여 홈페이지에 들어가 [virtualbox](https://www.virtualbox.org/)를 설치합니다.

가장 최신 버전인 v7.0.14로 설치하였습니다.

### 2. Vagrant를 설치합니다. 
Vagrant는 개발 환경을 구축하고 관리하기 위한 오픈 소스 소프트웨어입니다. 쉽게 말해 가상화 플랫폼(e.g. virtualbox) 가상 머신을 쉽게 생성, 구성, 관리할 수 있게 도와주는 도구라고 생각하시면 됩니다. [링크](https://developer.hashicorp.com/vagrant/install?product_intent=vagrant)를 첨부합니다.
{: note}

3. Vagrantfile을 이용해 가상머신을 구동시킵니다.

    ``` bash
    # 원하는 디렉토리 이동 후 입력
    vagrant init
    ```
    `vagrant init`을 입력하게 되면, Vagrantfile이 생깁니다. 이 Vagrantfile을 열어 아래의 내용을 복사, 붙여넣기 합니다.

    ``` text
    Vagrant.configure("2") do |config|
      config.vm.boot_timeout = 3000

      config.vm.define "master" do |master|
        master.vm.box = "ubuntu/focal64"
        master.vm.network "private_network", ip: "192.168.56.10"
        master.vm.hostname = "master"

        master.vm.provider "virtualbox" do |v|
          v.memory = 10240
          v.cpus = 10
        end
      end

      config.vm.define "worker1" do |worker1|
        worker1.vm.box = "ubuntu/focal64"
        worker1.vm.network "private_network", ip: "192.168.56.11" 
        worker1.vm.hostname = "worker1" 

        worker1.vm.provider "virtualbox" do |v|
          v.memory = 2048
          v.cpus = 2
        end
      end
  
      config.vm.define "worker2" do |worker2|
        worker2.vm.box = "ubuntu/focal64"
        worker2.vm.network "private_network", ip: "192.168.56.12" 
        worker2.vm.hostname = "worker2" 

        worker2.vm.provider "virtualbox" do |v|
          v.memory = 2048
          v.cpus = 2
        end
      end    
    end
    ```


이제 쿠버네티스 클러스터를 위한 가상머신 구축이 끝났습니다.


---
## Master 노드

가장 먼저, master 노드를 구성합니다.

### 1. Master  노드 shell 접속

  ``` bash
  vagrant ssh master
  ```

### 2. 네트워크 설정

- br_netfilter 셋업
  br_netfilter라는 요소를 활성화 합니다. 이 과정을 거쳐야 iptables가 bridge 인터페이스의 패킷을 제어하여 bridge 인터페이스를 사용하는 프로세스(pod, container 등) 간 통신이 가능해집니다.

  ``` bash
  sudo su
  echo "br_netfilter" > /etc/modules-load.d/br_netfilter.conf
  exit
  ```

- iptables에서 br_netfilter 사용 활성화
  iptables에서 앞에서 활성화한 br_netfilter를 사용하도록 설정합니다.
  이를 설정해주어야 컨테이너에서 발생한 패킷이 호스트의 iptables에서 제어됩니다.
  
  bridge-nf가 앞에서 로드한 모듈 br_netfilter를 의미합니다. {: note}

  ``` bash
  cat <<-'EOF' >/etc/sysctl.d/kubernetes.conf
  net.bridge.bridge-nf-call-iptables = 1
  net.bridge.bridge-nf-call-ip6tables = 1
  net.ipv4.ip_forward = 1
  EOF
  ```

  앞에서 설정한 내용을 적용하기 위해 아래의 명령어를 입력합니다.
  ``` bash
  sudo sysctl --system
  ```

### 3. 컨테이너 런타임 설치

다음으로 kubernetes에서 동작될 컨테이너 런타임을 설치합니다. 저는 `containerd`를 사용하였습니다.

  ``` bash
  sudo apt-get -y update

  sudo apt-get install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

  sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg

  sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

  sudo apt-get -y update

  sudo apt-get install -y containerd.io

  containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1

  sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

  sudo systemctl restart containerd

  sudo systemctl enable containerd
  ```

커맨드 입력을 통해 systemctl로 containerd를 실행하게 됩니다.

### 4. kubelet, kubectl, kubeadm 설치

설치에 들어가기 앞서, 각 구성요소의 역할을 간단하게 설명드리겠습니다.

- kubelet
  - 정의: `kubelet`은 각 노드에 할당된 pod가 실행되고 있는지 관리하고, kubernetes master와 통신하여 작업을 받습니다.
  - 역할: `kubelet`은 pod의 스펙을 모니터링하고, 상태를 확인한 다음 이 정보를 kubernetes master에 보고합니다. 또한, pod가 요구하는 리소스(container image 다운로드, 네트워크 설정 등)를 준비하고, container를 실행합니다.

- kubectl
  - 정의: `kubectl`은 kubernetes 클러스터를 조작하기 위한 CLI 도구입니다.
  - 역할: 사용자는 `kubelet`을 사용하여 애플리케이션을 배포하고, cluster 리소스를 검사 및 관리하며, 로그를 확인할 수 있습니다.

- kubeadm
  - 정의: `kubeadm`은 kubernetes 클러스터를 쉽게 구성하는데 사용되는 도구입니다.
  - 역할: `kubeadm init`과 `kubeadm join` 명령을 사용하여 클러스터의 master 노드와 worker 노드를 설정할 수 있습니다. `kubeadm`은 kubernetes 클러스터의 구성, 클러스터 인증서 생성, `kubelet` 설정 등의 작업을 자동화하여 사용자가 kubernetes 클러스터를 쉽게 구축할 수 있도록 도와줍니다.

``` bash
sudo su

# Master 노드 IP 설정
cat <<-'EOF' >/etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.56.10
EOF

exit
```

아래의 커맨드를 입력하여 `kubelet`, `kubeadm`, `kubectl` 설치를 진행합니다. 가장 최신의 버전으로 kubernetes를 구축하게 됩니다. [쿠버네티스 공식 문서](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)를 바탕으로 설치하였습니다. 

``` bash
sudo apt-get update

# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | 

sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

다음을 수행한 후 apt-get update를 진행합니다. 그 후 최신 버전의 `kubelet`, `kubeadm`, `kubectl`을 설치합니다.

``` bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### 4. 클러스터 생성

``` bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint=192.168.56.10 --apiserver-advertise-address=192.168.56.10
```

커맨드를 실행하게 되면 아래 사진과 같은 결과가 출력됩니다.

결과들 중 다음 정보가 중요합니다.

![800x400](/assets/img/blog/0312-k8s-configuration.png "K8s configuration")

아래의 커맨드를 입력하여 master 노드가 클러스터를 컨트롤 할 수 있는 권한을 부여합니다.

``` bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 5. Network add-on: flannel 설치

`Flannel`은 pod간 통신이 가능하도록 도와주는 CNI(Container Network Interface)입니다. 다음을 입력하여 `flannel`을 설치합니다.

``` bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

`flannel`을 사용하기 위해 kubeadm init 명령어 시 --pod-network-cidr=10.244.0.0/16을 입력하였습니다. `flannel`의 inet 부분도 10.244.0.0으로 동일합니다. 

---

## Worker 노드

Worker 노드에서 `kubeadm join`으로 앞에서 구축한 클러스터에 가입합니다.

그 전에 필요한 요소를 먼저 설치합시다. **Worker 노드 모두 접속하여 다음의 작업을 각각 수행해야 합니다.**

### 1. Worker 노드 shell 접속
``` bash
vagrant ssh worker1 # 192.168.56.11
```

### 2. 네트워크 설정

Master 노드와 동일하게 셋팅을 진행합니다.

- br_netfilter 셋업
``` bash
sudo modprobe br_netfilter
```

- iptables에서 br_netfilter 사용 활성화
``` bash
cat <<-'EOF' >/etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF
```
``` bash
sudo sysctl --system
```

### 3. 컨테이너 런타임 설치
``` bash
sudo apt-get -y update
sudo apt-get install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get -y update
sudo apt-get install -y containerd.io

containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd
```

### 4. kubelet, kubeadm 설치
``` bash
cat <<-'EOF' >/etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.56.10
EOF

sudo apt-get update

# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | 

sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

다음을 입력해 `kubelet`, `kubelet`을 설치합니다.

``` bash
sudo apt-get install -y kubelet kubeadm 
sudo apt-mark hold kubelet kubeadm 
```

### 5. Master 노드에서 kubeadm init시 생성된 join 명령문을 실행합니다.

성공적으로 join이 되었다면 아래와 같은 출력이 뜨게 됩니다.
``` bash
This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

RUN `kubectl get nodes` on the control-plane to see this node join the cluster.
```

---

## Cluster 구성 확인
Master 노드에서 아래의 커맨드를 입력합니다.
``` bash
kubectl get node
```

모든 노드가 Ready 상태라면 성공적으로 클러스터가 구축된 것입니다.
![400x200](/assets/img/blog/0312-k8s-configuration-confirm.png "Large example image")
