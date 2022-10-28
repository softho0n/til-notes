# Change `data-root` path

- Docker 이미지, 볼륨, 클러스터 정보 등을 저장하는 기본 경로는 `/var/lib/docker` 로 설정된다.
- 다만 루트 혹은 `/var` 파티션의 용량이 부족할 경우와  더 빠른 nvme를 저장소로 사용하고 싶은 경우 재설정이 필요하다.
- 이번 문서에서는 Docker의 artifacts를 저장하는 저장소 위치를 변경하는 방법에 대해 정리하였다.

### Docker 서비스 일시중단 시키기

- `data-root` 를 변경하기 위해 우선적으로 실행 중인 docker 서비스를 내려야 한다.
    
    ```bash
    systemctl stop docker.socker
    systemctl stop docker
    
    systemctl status docker
    ```
    

### `data-root` 변경

- data-root 및 shm 용량 수정을 위해서 `/etc/docker/daemon.json` 파일을 수정한다.
    
    ```bash
    vim /etc/docker/daemon.json
    ```
    
- `daemon.json` 파일 내 아래 키/값을 추가해준다.
    
    ```json
    {
    	"data-root": "your_directory",
    	"default-shm-size": "1G" 
    }
    ```
    
- 도커 서비스를 재시작한다.
    
    ```bash
    service docker start
    systemctl status docker
    ```