# Remote branch 가져오기

- 원격 저장소에 있는 branch를 로컬 저장소에 가져와야하는 상황이 발생할 수 있음.
- 저장소를 clone 할지라도 원격 저장소의 branch를 같이 pull 하지는 않는다.

## git remote update

- 원격 브랜치에 접근하기 위해 git remote를 갱신 해주어야 한다.
    
    ```bash
    $ git remote update
    ```
    
- 갱신 뒤 정상적으로 `git branch -r` 명령어를 입력하면 원격 저장소가 갱신된 것을 확인할 수 있다.
- `git branch -a` 명령어를 통해 로컬/원격 저장소에 존재하는 모든 branch 확인도 가능하다.

## 원격 저장소 branch 가져오기

- 먼저 `git branch -r` 을 통해 원격 저장소의 브랜치 리스트를 확인한다.
    
    ```bash
    $ git branch -r
      origin/HEAD -> origin/develop
      origin/develop
      origin/feat/channel
      origin/feat/streaming
    ```
    
- 위 상황에서 `origin/feat/streaming` 브랜치를 가져오고 싶다고 가정해보자.

- -t 옵션과 원격 저장소의 branch 이름을 입력하면 로컬의 동일한 이름의 branch를 생성하면서 checkout을 진행.
    - `git checkout -t origin/feat/streaming`

- 만약 branch 이름을 변경하고 싶다면,
    - `git checkout -b [생성하고 싶은 branch 명] [원격 저장소의 branch 명]` 으로 가면 된다.
        - `git checkout -b feat/mybranch origin/feat/streaming`