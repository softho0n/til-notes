# How to install docker?

- 쿠버네티스를 설치하기 전 컨테이너 런타임 설치가 선행되어야 한다.
- 대표적인 컨테이너 런타임인 Docker 런타임을 설치하는 과정에 대해 알아보자.
- 순서대로 진행하면 된다.

### 사전준비

- 패키지 업데이트
    
    ```bash
    apt update -y && apt upgrade -y
    ```
    
- 스왑 메모리 해제
    
    ```bash
    sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
    swapoff -a
    ```
    
    → 혹시나 나중에 쿠버네티스를 통해 클러스터를 구성하는 상황에 대비해서 필수적으로 진행하자.
    

### 도커 설치하기

- 도커 설치에 필요한 패키지 다운로드
    
    ```bash
    sudo apt-get update
    
    sudo apt-get install \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
    ```
    
    → HTTPS를 통해 도커 Repository에 접근할 것이므로, 접근에 필요한 패키지들을 다운로드 받는다.
  
    
- 도커 공식 GPG Key 등록
    
    ```bash
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    ```
    
- 도커 Repository 설정
    
    ```bash
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```
    
- 도커 설치
    
    ```bash
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```
    
- 도커 cgroup driver 설정
    - 이는 권장사항인데 쿠버네티스는 `systemd` 드라이버 사용을 추천하고 있다.
    
    ```bash
    cat <<EOF | sudo tee /etc/docker/daemon.json
    {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
        "max-size": "100m"
      },
      "storage-driver": "overlay2"
    }
    EOF
    ```
    

### NVIDIA-docker 설치 (Optional)

- 만약 도커에서 GPU 사용을 희망한다면 아래 내용을 참고하자.
    
    ```bash
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
    
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    
    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list
    
    sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
    
    sudo systemctl restart docker
    ```
    

### 방화벽 및 네트워크 환경설정 (Optional)

- 추후 쿠버네티스 환경에서 노드 간 CNI를 통해 통신을 하게 될 것이다.
- 이를 위해 네트워크 및 방화벽 환경 설정을 진행하도록 하자
    
    ```bash
    modprobe overlay
    modprobe br_netfilter
    
    cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    EOF
    ```