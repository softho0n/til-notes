# Useful Commands

## conda 가상환경 리스트 확인하기

- `conda info --envs`
    
    ```bash
    conda info --envs
    
    base                  *  /home/jason/miniconda3
    airflow                  /home/jason/miniconda3/envs/airflow
    dp                       /home/jason/miniconda3/envs/dp
    dp-infra                 /home/jason/miniconda3/envs/dp-infra
    flask                    /home/jason/miniconda3/envs/flask
    flask-auth               /home/jason/miniconda3/envs/flask-auth
    ```
    

## conda 가상환경 삭제하기

- `conda remove -n <env-name> --all`
    
    ```bash
    conda remove -n kfp --all
    
    Remove all packages in environment /home/jason/miniconda3/envs/kfp:
    
    ## Package Plan ##
    
      environment location: /home/jason/miniconda3/envs/kfp
    
    The following packages will be REMOVED:
    
      _libgcc_mutex-0.1-main
      _openmp_mutex-5.1-1_gnu
      asttokens-2.0.5-pyhd3eb1b0_0
      backcall-0.2.0-pyhd3eb1b0_0
      ca-certificates-2022.07.19-h06a4308_0
      certifi-2022.9.24-py38h06a4308_0
      debugpy-1.5.1-py38h295c915_0
    ```
    

## conda 가상환경 생성하기

- `conda create -n <env-name> python=<python-version>`
    
    ```bash
    conda create -n dataflow python=3.8
    ```
    

## conda 가상환경 활성/비활성 하기

- `conda activate <env-name>`
    
    ```bash
    conda activate dataflow
    ```
    
- `conda deactivate`
    
    ```bash
    (dataflow) conda deactivate
    ```
    

## conda 가상환경 이름 변경

- `conda create -n <new_name> --clone <old_name>`
- `conda remove -n <old_name> --all -y`