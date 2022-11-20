# Git Branch command

- branch 생성 및 제거, 확인을 하기 위한 명령어이다.
- `git branch -l`
    - 로컬 branch 리스트를 확인할 수 있다.
    
    ```bash
    $ git branch
      develop
    * feat/terraform_bigquery
    ```
    
- `git branch -v`
    - 로컬 branch 리스트를 마지막 커밋 메시지와 함께 볼 수 있다.
    
    ```bash
    $ git branch -v
      develop                 9e2669e fix: Fix terraform codes for production (#22)
    * feat/terraform_bigquery 9e2669e fix: Fix terraform codes for production (#22)
    ```
    
- `git branch -r`
    - 리모트 저장소의 branch를 확인할 수 있다.
    
    ```bash
    $ git branch -r
      origin/HEAD -> origin/develop
      origin/develop
      origin/feat/aid-channel-stream
      origin/feat/terraform-iam
      origin/feature/terraform-backend
      origin/feature/terraform-base
      origin/fix/aid-batch
      origin/main
    ```
    
- `git branch -a`
    - 로컬과 리모트 저장소의 모든 branch 정보를 확인할 수 있다.
    
    ```bash
    $ git branch -r
      develop
    * feat/terraform_bigquery
      remotes/origin/HEAD -> origin/develop
      remotes/origin/develop
      remotes/origin/feat/aid-channel-stream
      remotes/origin/feat/terraform-iam
      remotes/origin/feature/terraform-backend
      remotes/origin/feature/terraform-base
      remotes/origin/fix/aid-batch
      remotes/origin/main
    ```
    
- `git branch -d`
    - branch를 삭제한다.
    - merge되지 않은 커밋 메시지가 존재하는 경우 branch인 경우 삭제가 불가능하다.
    - -D 옵션을 통해 강제로 브랜치 삭제는 가능하다.
- `git branch -m <A> <B>`
    - A branch를 B branch로 이름을 변경한다.