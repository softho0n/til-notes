# Driver/library version mismatch 문제 해결 방법

- `nvidia-smi` 명령어를 통해 정상적으로 GPU 디바이스를 확인할 수 있었으나,
- `Driver/library version mismatch` 메시지가 갑자기 발생하는 경우에 대해 해결하는 방법을 정리하였음.

## 원인 분석

- 아래 명령어를 통해 오류 메시지를 확인할 수 있다.
    
    ```bash
    $ dmesg
    ```
    
- NVRM 항목을 찾는다.
    - API mismatch 오류 메시지를 발견할 수 있는데 클라이언트 버전과 커널 모듈의 버전 차이가 발생해 에러가 발생함을 확인할 수 있을 것이다.
    - 이는 리눅스 OS의 unattended-upgrade 프로세스가 보안 관련 패키지를 자동으로 업데이트해 버전 차이가 발생하는 것이다.
- `unattended-upgrade` 로그 확인
    - `/var/log/unattended-upgrades` 에서 확인할 수 있다.
        
        ```bash
        cat /var/log/unattended-upgrades/unattended-upgrades.log.1.gz
        ```
        
    - 아래 로그에서 볼 수 있듯이 nvidia 관련 패키지들이 자동 업데이트 되었다.
        
        ```bash
        2022-12-05 06:31:20,670 INFO Packages that will be upgraded: libnvidia-compute-470
        2022-12-05 06:31:20,670 INFO Writing dpkg log to /var/log/unattended-upgrades/unattended-upgrades-dpkg.log
        2022-12-05 06:31:35,160 INFO All upgrades installed
        ```
        

## 해결 방안

### 자동 업데이트 방지

- unattended-upgrade의 대상 패키지에서 nvidia 관련 패키지를 제외한다.
    - 즉, 자동 업데이트를 방지하는 것이다.
- `/etc/apt/apt.conf.d/50unattended-upgrades` 파일을 아래와 같이 수정한다.
    
    ```bash
    vim /etc/apt/apt.conf.d/50unattended-upgrades
    
    Unattended-Upgrade::Package-Blacklist {
      "nvidia-*.";
    }
    ```
    

### nvidia 모듈 삭제

- 이미 자동 업데이트 되어 설치가 되었다면 기존 모듈을 삭제하면 된다.
- 먼저 커널 모듈을 확인한다.
    
    ```bash
    lsmod | grep nvidia
    
    # 출력된 모듈 삭제
    rmmod nvidia_uvm
    ```
    
- 만약 `ERROR: Module nvidia is in use` 가 발생한다면 해당 프로세스를 종료시킨다.
    
    ```bash
    lsof /dev/nvidia*
    kill -9 $PID
    ```