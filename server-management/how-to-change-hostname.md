# How to change hostname?

- 현재 Ubuntu 환경에서 storage, master, gpu1, gpu2 총 네 개의 노드를 가진 클러스터를 구축했다고 가정해보자.
- Secure Shell을 통해 각 노드에 root 권한으로 접속을 하게 된다면 아래와 같은 상태를 확인할 수 있을 것이다.
    
    ```bash
    root@master:~$
    
    root@storage:~$
    
    root@gpu1:~$
    
    root@gpu2:~$
    ```
    
- 노드 이름의 통일성이 보장되지 않았기에 나중 서버 유지/보수에 있어 단점이 존재한다.
- 실제 클러스터를 구축하게 되면 협업을 위해 클러스터 이름을 정하게 되는데, 이 시점에 hostname을 변경하는 방법을 알고 있어야 한다.
- 여기선 클러스터 이름을 softhoon으로 정했다고 가정하겠다.
- 실제 hostname을 변경하는 명령어는 아래와 같다.
    
    ```bash
    root@master:~$ hostnamectl set-hostname softhoon-master
    
    root@storage:~$ hostnamectl set-hostname softhoon-storage
    
    root@gpu1:~$ hostnamectl set-hostname softhoon-gpu1
    
    root@gpu2:~$ hostnamectl set-hostname softhoon-gpu2
    ```
    
- 위 명령어를 실행하고 재접속하게 되면 아래와 같이 변경이 적용 되었을 것이다.
    
    ```bash
    root@softhoon-master:~$
    
    root@softhoon-storage:~$
    
    root@softhoon-gpu1:~$
    
    root@softhoon-gpu2:~$
    ```