# Configuration k8s cluster

- 쿠버네티스 클러스터 환경 설정 및 설치하는 내용에 대해 다룹니다.
- 구글 GKE를 사용하는 것이 아닌 실제 Compute Engine 상에서 노드를 생성해서 진행합니다.
- 컨테이너 런타임은 docker를 사용한다고 가정합니다.

### `kubeadm`, `kubelet`, `kubectl` 설치하기

- 쿠버네티스 설치를 위해선 `kubeadm`, `kubelet`, `kubectl` 등이 필요합니다.
- `kubeadm` : 클러스터를 부트스트랩하는 명령입니다. 클러스터를 묶을 때 실제로 사용됩니다.
- `kubelet` : 클러스터의 모든 머신에서 실행되는 파드와 컨테이너 시작과 같은 작업을 수행합니다.
- `kubectl` : 클러스터와 통신하기 위한 커맨드 라인 유틸리티입니다.  
  

- 패키지 업데이트 및 설치 시 필요한 패키지 다운로드
    
    ```bash
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl
    ```
    
- 구글 클라우드의 공개 사이닝 키 다운로드
    
    ```bash
    sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
    ```
    
- 쿠버네티스 Repository 추가
    
    ```bash
    echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    ```
    
- kubeadm, kubelet, kubectl 설치
    - 현재 1.21.12 버전이 안정적이므로 해당 버전으로 설치를 진행하겠습니다.
    
    ```bash
    sudo apt-get update
    sudo apt-get install -y kubelet=1.21.12-00 kubeadm=1.21.12-00 kubectl=1.21.12-00
    sudo apt-mark hold kubelet kubeadm kubectl
    ```
    

### 쿠버네티스 클러스터 구성하기

- `kubeadm`을 통해 실제로 클러스터를 구성할 수 있습니다.
- 먼저 마스터 노드에서만 아래 명령을 먼저 실행하도록 합시다.
    
    ```bash
    kubeadm init
    ```
    
- 위 명령어를 실행하면 두 가지 타입의 명령어가 생성되는데 첫 번째 명령어는 아래와 같습니다.
    
    ```bash
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```
    
- 두 번째 명령어는 아래와 같습니다. 이 명령어는 **마스터 노드가 아닌 워커 노드에서 실행하도록 합니다.**
    
    ```bash
    kubeadm join 100.100.100.3:6000 --token aaaba.amj9qwerxcd5rsao \
    	--discovery-token-ca-cert-hash sha256:123412342340172d730bf0a648052dwer63143a8342136batret9570
    ```
    
- 위 명령어들을 정상적으로 실행시켰다면 클러스터 구성이 완성됩니다.

### 클러스터 CNI 설치 & 클러스터 상태 확인하기 & GPU device plugin 설치

- 정상적으로 워커 노드가 조인 되었는지 확인하기 위해선 아래 명령어를 실행하면 됩니다.
    
    ```bash
    kubectl get nodes
    
    NAME     STATUS   ROLES                  AGE   VERSION
    gpu1     Ready    <none>                 29h   v1.21.12
    gpu2     Ready    <none>                 29h   v1.21.12
    gpu3     Ready    <none>                 10h   v1.21.12
    gpu4     Ready    <none>                 29h   v1.21.12
    gpu5     Ready    <none>                 29h   v1.21.12
    master   Ready    control-plane,master   29h   v1.21.12
    ```
    
- 구성한 클러스터 내에 CNI(Container Network Interface)를 설치해서 Pod 간 통신이 가능하게 해줍니다.
    
    ```bash
    kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
    ```
    
- 쿠버네티스 환경에서 GPU 사용을 원한다면 아래 플러그인을 설치해야합니다.
    
    ```bash
    kubectl create -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v0.12.2/nvidia-device-plugin.yml
    ```