# How to create new account in kubeflow?

- 쿠버네티스 환경에서 작동하는 MLOps 시스템인 `kubeflow`에 새로운 유저를 등록하는 법에 대해 정리함.
- `kubeflow` 내 account는 `dex` 기반으로 관리함.
- 이 문서에서는 사용자 목록을 조회하는 방법, 추가하는 법에 대해 알아봄.

### Check all user lists

- 사용자 정보를 파일에 직접 저장하고 있음
- 아래 스크립트를 통해 현재 유저 정보 리스트에 대해 확인이 가능함.
    
    ```bash
    kubectl get cm dex -n auth -o yaml
    
    # staticPasswords 필드에 유저 정보가 담겨있음.
    logger:
      level: "debug"
      format: text
    oauth2:
      skipApprovalScreen: true
    enablePasswordDB: true
    staticPasswords:
    - email: user@example.com
      hash: $2b$10$y66vBffewdz2131ZUjARMOCGzXp0Vyp0OrbiU2ybn0CS
      username: test
      userID: "test"
    
    ```
    

### Add user

- 다음 명령어를 통해 사용자를 직접 추가할 수 있다.
    
    ```bash
    kubectl -n auth edit cm dex
    ```
    

- `staticPasswords` 필드에 새로운 사용자를 아래와 같이 추가한다.
    
    ```bash
    staticPasswords:
        - email: user@example.com
          hash: 12$ruoM7FqXrpVgaol44eRZW.4HWS8SAvg6KYVVSCIwKQPBmTpCm.EeO
          username: user
          userID: "15841185641784"
        - email: your_account@your_mail.com
          hash: your_hashing_password
          username: your_name
          userID: "your_name"
    ```
    
    → `hash` 필드에는 실제 비밀번호를 암호화한 값을 입력해야한다.
    
    → 즉, 아래와 같이 파이썬 스크립트를 통해 패스워드를 해시값으로 변환해준다.
    
    ```bash
    pip install bcrypt
    
    python3 -c 'import bcrypt; print(bcrypt.hashpw(b"your_password", bcrypt.gensalt(rounds=10)).decode("ascii"))'
    
    # YOUR_HASHED_PASSWORD_OUTPUT 값을 이용해 위 필드에 기입해주면 된다.
    YOUR_HASHED_PASSWORD_OUTPUT
    ```
    

### Configuration namespace auto-generating options

- `dex` 에 정상적으로 유저를 추가하고 로그인을 하게 되면 고립된 환경을 얻을 수 있다.
- 실제 `kubeflow` 에서 작업들은 namespace 단위로 분리된다.
- 새로운 계정 생성 시 namespace 또한 자동으로 생성 시켜주는 옵션을 설정해주면 더 용이하다.
    
    ```bash
    cd <manifests-path>/apps/centraldashboard/upstream/base/
    
    vim params.env
    
    CD_CLUSTER_DOMAIN=cluster.local
    CD_USERID_HEADER=kubeflow-userid
    CD_USERID_PREFIX=
    CD_REGISTRATION_FLOW=true
    ```