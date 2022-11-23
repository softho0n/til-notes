# How to edit commit message

## case1. 가장 최근 커밋 메시지 수정하기

- 파일 하나를 아래와 같이 추가함.
    
    ```bash
    ➜  test git:(main) touch 2
    ```
    
- `git status` 로 상태를 확인함.
    
    ```bash
    ➜  test git:(main) ✗ git status
    On branch main
    Your branch is ahead of 'origin/main' by 1 commit.
      (use "git push" to publish your local commits)
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    	2
    
    nothing added to commit but untracked files present (use "git add" to track)
    ```
    
- `git add` 후 `commit` 진행 남김.
    
    ```bash
    ➜  test git:(main) ✗ git add 2; git commit -m "Add 2"
    [main 037d053] Add 2
     1 file changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 2
    ```
    
- `git log` 로 확인함.
    
    ```bash
    ➜  test git:(main) ✗ git log
    commit 037d053ebab87fc611c4fd6af2be1184a3422ba6 (HEAD -> main)
    Author: Seunghun Shin <42256738+softho0n@users.noreply.github.com>
    Date:   Wed Nov 23 00:46:19 2022 +0900
    
        Add 2
    ```
    
- 이 상황에서 commit 메시지를 수정하고 싶을때?
    
    ⇒ `git commit --amend` 를 활용하면 된다.
    
    ⇒ 즉, 로컬에서 commit을 하고 push는 하지 않아 원격 저장소에 반영되지 않은 상태에서 사용할 수 있다.
    
    ```bash
    ➜  test git:(main) ✗ git commit --amend
    
    Add 2 new
    ```
    

## case2. 더 오래된 commit 수정 또는 여러 commit 수정

- `git rebase -i HEAD~<number>`
- `HEAD` 로부터 최근 `number` 개 만큼 commit을 한꺼번에 수정할 수 있음.

## case3. 이미 remote에 올라간 경우

- 일단 로컬에서 `amend` 혹은 `rebase` 를 통해서 커밋을 수정함.
- `force` 옵션을 부여하고 `push` 한다.
    
    `git push --force <branch-name>`