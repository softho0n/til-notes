# 우분투 환경에서 NFS 구축하기

- 이번 문서에서는 Linux 환경에서 Network File System을 Mount하는 법을 정리하였다.
- 클러스터를 구축하게 되면 storage 서버를 독립적으로 마련하게 된다.
- 모든 워커 노드들의 저장소를 특정 storage 서버 노드로 정의할 때 필요한 작업 중 하나이다.

## NFS Server Configuration

- 스토리지 서버 혹은 저장소로 사용할 곳에서 진행할 작업이다.
- 서버 호스트는 `100.100.100.0` 이라 가정한다.
- NFS 패키지를 설치해준다.
    
    ```bash
    sudo apt-get install nfs-kernel-server
    ```
    
    - 위 명령어 실행 시, nfs-common, rpcbind 등의 추가 패키지는 같이 설치된다.
- NFS 클라이언트가 바라보게 될 공유 디렉토리를 정의한다.
    
    ```bash
    # /mnt/nfs-share 디렉토리를 클라이언트가 바라보게 될 것임.
    sudo mkdir -p /mnt/nfs-share
    sudo chmod 777 /mnt/nfs-share
    
    ```
    
- 접근 가능한 클라이언트 정보를 exports 파일에 추가한다.
    - `100.100.100.1`, `100.100.100.2`, `100.100.100.3`,`100.100.100.4`
    - 위 네 개의 클라이언트가 있다고 가정
    
    ```bash
    vim /etc/exports
    
    # (공유할 디렉토리 Path) 클라이언트 HostIP (Permissions list)
    /mnt/nfs-share 100.100.100.1/24(rw,sync,no_root_squash,no_subtree_check)
    /mnt/nfs-share 100.100.100.2/24(rw,sync,no_root_squash,no_subtree_check)
    /mnt/nfs-share 100.100.100.3/24(rw,sync,no_root_squash,no_subtree_check)
    /mnt/nfs-share 100.100.100.4/24(rw,sync,no_root_squash,no_subtree_check)
    ```
    
    - 권한 옵션은 다양하다.
        - rw: read write
        - ro: read only
        - sync: 파일 시스템이 변경되면 즉시 동기화
        - all_squash: root를 제외한 서버/클라이언트 사용자를 동일한 권한으로 설정
        - no_root_squash: 클라이언트의 root로 접근을 허용
- 클라이언트 정보 적용 및 nfs server 재시작
    
    ```bash
    sudo exportfs -a
    sudo systemctl restart nfs-kernel-server
    ```
    

## NFS Client Configuration

- 실제 클라이언트 노드에서 진행할 작업이다.
- NFS 클라이언트 패키지를 설치해준다.
    
    ```bash
    sudo apt-get install nfs-common
    ```
    
- NFS 서버의 공유 디렉토리를 직접 마운트할 디렉토리를 생성한다.
    
    ```bash
    # 위 서버의 /mnt/nfs-share 디렉토리와 통신하기 위한 클라이언트 로컬 디렉토리를 생성하는 단계
    sudo mkdir -p /mnt/nfs-client
    ```
    
- 클라이언트 호스트IP 주소를 알아낸다.
    
    ```bash
    ifconfig
    
    # inet 필드 값
    ens82f0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 100.100.100.1  netmask 100.100.100.1  broadcast 100.100.100.1
            inet6 fe80::d250:99ff:fedd:14a3  prefixlen 64  scopeid 0x20<link>
    ```
    
- 마운트를 진행한다.
    
    ```bash
    # mount server-ip:<SERVER_NFS_DIR_PATH> <CLIENT_NFS_DIR_PATH>
    sudo mount 100.100.100.0:/mnt/nfs-share /mnt/nfs-client
    ```
    
- 정상적으로 마운트가 되었는지 확인한다.
    
    ```bash
    df -h
    
    # 아래와 비슷한 형태로 나온다면 정상적으로 마운트가 되었을 것이다.
    Filesystem           Size  Used Avail Use% Mounted on
    100.100.100.1:/data  164T   69T   95T  43% /data
    tmpfs                 13G   20K   13G   1% /run/user/125
    ```