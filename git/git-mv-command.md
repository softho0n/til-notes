# `git mv` command

- 로컬 저장소에서 작업을 수행하다 파일 이름을 변경하거나 경로를 변경할 순간이 있을 수도 있음.
- 간단한 명령어를 통해 파일 이름 변경과 경로 변경을 쉽게 수행하고 history로 남길 수 있음.
- 테스트를 위한 파일을 하나 생성함.
    
    ```bash
    touch test_file
    
    git add test_file
    
    git commit -m "add test_file"
    
    git push
    ```
    

- ***파일 이름을 변경하고 싶은 경우에는?***
    
    ```bash
    git mv test_file changed_test_file
    
    git commit -m "change name of test_file to changed_test_file"
    
    git push
    ```
    

- ***파일의 경로를 옮기고 싶은 경우에는?***
    
    ```bash
    mkdir new_dir
    
    git mv test_file ./new_dir/
    
    git commit -m "change file path"
    
    git push
    ```
    
- ***디렉토리 이름을 바꾸고 싶은 경우에는?***
    
    ```bash
    mkdir new_dir; cd new_dir; touch 1; cd ../
    
    git add new_dir/
    
    git commit -m "add new dir"
    
    git push
    
    git mv new_dir/ changed_dir/
    
    git commit -m "change name of new_dir to changed_dir"
    
    git push
    ```