# How to delete files or directory in git?

- 임의의 파일을 하나 생성하고 push 했다고 가정해보자.
    
    ```bash
    touch 1
    
    git add 1
    
    git commit -m "add 1"
    
    git push
    ```
    
- 원격 저장소에 파일 1이 정상적으로 업로드 되었을 것이다.
- 하지만, 이 파일을 삭제하고 싶은 경우가 발생할 수도 있다.
- git 상에서는 파일을 삭제하고 commit을 반드시 진행 해주어야 한다.

### 로컬 저장소와 원격 저장소에서 모두 삭제

- 삭제하려는 대상 파일을 로컬 저장소와 원격 저장소 두 군데에서 동시에 삭제하는 명령어는 아래와 같다.
- `git rm <filename>`
- 실제 사용 예제는 아래와 같다.
    
    ```bash
    git rm 1
    
    git commit -m "delete 1"
    
    git push
    ```
    
- `push` 를 해줘야지만 정상적으로 원격 저장소에서 파일이 삭제된다.

### 로컬 상에서는 유지하고 원격 저장소에서만 삭제

- 원격 저장소에서만 파일을 삭제할 경우는 아래와 같다.
- `git rm --cached <filename>`
- 실제 사용 예제는 아래와 같다.
    
    ```bash
    git rm --cached 1
    
    git commit -m "delete 1 only remote"
    
    git push
    ```
    
- `push` 를 해줘야지만 정상적으로 원격 저장소에서 파일이 삭제된다.

### 폴더 삭제

- 폴더 삭제도 크게 다를게 없음
    
    ```bash
    git rm -rf <dir>
    
    git commit -m "delete directory"
    
    git push
    ```
    
    ```bash
    git rm --cached -rf <dir>
    
    git commit -m "delete directory only remote"
    
    git push
    ```