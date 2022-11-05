# Secure Shell config file

- Secure shell을 통해 원격 접속으로 서버환경에서 작업하는 경우가 많다.
- 호스트명, 포트, 유저네임을 항상 입력해서 접속하는 것은 번거로운 일이다.
- 이번 문서에서는 Secure shell을 더 손 쉽게 접속하기 위해 설정하는 방법에 대해 정리하였다.
- 먼저 `ssh-keygen` 을 통해 공개 키를 발급 받는다.
    
    ```bash
    ssh-keygen
    ```
    
- 그 뒤, `~/.ssh` 디렉토리로 이동한다.
    
    ```bash
    cd ~/.ssh
    ```
    
- 해당 경로에서 `config` 파일을 생성해준다.
    
    ```bash
    vim config
    ```
    
- `config` 파일에 서버 접속 관련 정보를 아래와 같이 기입해준다.
    
    ```bash
    Host test
        HostName 100.100.100.1
        User jason
        Port 1110
    ```
    
- 위와 같이 `config` 파일에 내용을 추가할 경우 아래와 같이 접속이 가능해진다.
    
    ```bash
    # 기존 방식
    ssh -p1110 jason@100.100.100.1
    
    # config 파일을 설정했을 경우 (Host 명으로 접속 가능)
    ssh test
    ```
    
- (Optional) 서버에 엔트리 서버가 존재할 경우 아래와 같이 설정을 하면 된다.
    
    ```bash
    Host my_real_server # my server
        HostName 10.12.512.3
        User jason
        ProxyJump entry # entry 서버에 대한 Host를 지정하면 됨.
    
    Host entry # entry server
        HostName 192.151.351.2
        User entry_guest
        Port 2311
    ```