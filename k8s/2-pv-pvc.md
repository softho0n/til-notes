# Kubernetes PVC & PV

## PersistentVolume & PersistentVolumeClaim

- PV 리소스는 **쿠버네티스 클러스터 외부 스토리지와 연결을 담당하는 리소스**이다.
- PVC 리소스는 **PV와 생성하려는 Pod를 연결하기 위한 리소스**이다.
- 즉, 외부 저장소에 Pod에서 생성되는 데이터를 영구적으로 저장 및 로드하기 위해 필요하다.
- **Storage ↔️ PersistentVolume ↔️ PersistentVolumeClaim ↔️ Pod** 의 플로우를 따른다.
- PV 리소스는 네임 스페이스에 독립적이며, PVC 리소스는 네임 스페이스에 종속성을 가진다.

## How to make PersistentVolume?

- PV 리소스에서 정의할 수 있는 속성은 아래와 같다.
    - capacity
        - 사용량 용량
    - accessMode
        - ReadWriteOnce : 하나의 Pod에서만 읽고 쓰기 가능
        - ReadOnlyMany : 여러 개의 Pod에서 읽기 가능
        - ReadWriteMany : 여러 개의 Pod에서 읽고 쓰기 가능
    - volumeMode
        - FileSystem
    - storageClassName
        - 스토리지 클래스 지정
    - persistentVolumeReclaimPolicy
        - 볼륨 사용이 종료될 시점에 hostPath의 볼륨 삭제 여부 판단
    - hostPath
        - 실제 데이터가 저장될 스토리지 노드의 특정 디렉토리 경로
- PV 리소스 선언의 간단한 예제는 아래와 같다.
    
    ```yaml
    # NFS Version
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: test-pv
    spec:
      capacity:
        storage: 200Gi
      volumeMode: Filesystem
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      storageClassName: nfs
      nfs:
        path: /mnt/k8s-vol/
        server: 100.100.100.1
    ```
    
- 위와 같이 PV 리소스를 성공적으로 선언했다면 나중에 PVC 리소스와 연결을 통해 Pod 내에서 데이터 쓰기 작업을 수행할 경우 모든 데이터는 `/mnt/k8s-vol` 하위 디렉토리에 저장될 것이다.

## How to make PersistentVolumeClaim?

- Pod에서 미리 선언된 PV를 통해 접근하기 위해선 PVC 리소스 선언이 필요하다.
- 각 유저마다 PV 리소스 접근을 부여해주는 과정이라고 생각하면 간단하다.
- PVC 리소스 선언의 간단한 예제는 아래와 같다.
    
    ```yaml
    # 위 PV가 test-pv로 선언되었기 때문에 아래 volumeName을 test-pv로 일치 시켜주어야 함.
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: test-pvc
      namespace: jason
    spec:
      resources:
        requests:
          storage: 200Gi
      volumeMode: Filesystem
      volumeName: test-pv
      accessModes:
        - ReadWriteMany
    ```
    
- 위 PVC 리소스 선언을 통해 test-pv의 이름을 가지는 PV 리소스와 직접적인 연결이 완료된다.
- 나중 Pod를 선언할 때에는 test-pvc라는 PVC 리소스를 통해 데이터 로드 및 쓰기 작업을 수행하면 된다.

## How to use PVC in Pod?

- PV와 PVC 리소스 생성은 잘 되었고, 이제 Pod에서 어떻게 사용하는지 알아봐야한다.
- 아래는 간단한 Pod 예제이다.
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: www-vol
    	namespace: jason
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
          - mountPath: /mnt/ # Pod 내 실제 컨테이너에서의 마운트 경로
            name: pod-pvc # 아래 volumes.name 필드와 일치시켜야함!
      volumes:
        - name: pod-pvc # Pod 내에서 PVC를 부르는 이름을 다시 정의
          persistentVolumeClaim:
            claimName: test-pvc # 위에서 선언할 때 실제 PVC 리소스 이름과 일치해야함
    ```
    
- 해당 부분을 이해하기가 살짝 까다로웠는데 간단하게 설명하자면 아래와 같다.
- 먼저, `spec → volumes → persistentVolumeClaim → claimName`에는 위에서 PVC 리소스를 생성할 때 실제 PVC 리소스 이름을 기입해야한다.
- 그 다음, `spec → volumes → name`에는 Pod 내에서 해당 볼륨을 어떻게 부를 것인지 새로운 이름을 정해준다. 유저의 마음대로 정할 수 있다.
- `spec → containers → volumeMounts → name`에는 똑같이 `volumes → name`의 필드와 값을 일치시켜준다.
- 마지막으로, `spec → containers → volumeMounts → mountPath`에는 Pod가 생성이 되고 실제 컨테이너 내부에서 PVC 및 PV 리소스를 활용하기 위한 특정 경로를 지정해주면 된다.
- 만약, 위 Pod를 예로 들어 `/mnt` 경로에 데이터를 저장했다면 100.100.100.1 호스트의 `/mnt/k8s-vol/` 경로에 영구적으로 데이터가 저장될 것이다.