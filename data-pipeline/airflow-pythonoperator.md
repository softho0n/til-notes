# Airflow PythonOperator

- Airflow 상에서 컴포넌트를 구성할 때 각 컴포넌트를 Python 함수 단위로 구성할 수 있다.
- 사용하기 굉장히 편하며, Python에 이미 친숙한 상태라면 쉽게 적응할 수 있다.
- 먼저 실행하기 위한 로직을 Python 함수로 미리 정의한 뒤, `PythonOperator` 에 전달하면 된다.
- 반환 값을 다음 컴포넌트에서 그대로 사용할 수도 있으며, 매개변수 전달도 쉽게 가능하다.

## How to use?

- 먼저 어떻게 사용할까?
    
    ```python
    from airflow import models
    from airflow.operators.python import PythonOperator
    
    def this_is_your_function():
        print("Hello world")
    
    with models.DAG(
        "python-operator-1",
    ) as dag:
    
       python_component = PythonOperator(
           task_id="first_pythonoperator",
           python_callable=this_is_your_function
       )
       
       (
           python_component
       )
    ```
    
- 실행하기 위한 함수를 먼저 선언한 뒤, `PythonOperator` 의 `python_callable` 에 전달해주면 된다.

## How to make pipeline using PythonOperator?

- 여러 개의 함수를 연결해서 사용하는 경우가 필수적이다.
- 하나의 함수만 선언해서 사용할 이유가 크게 존재하지 않는다.
- `PythonOperator`를 이용해 파이프라인을 만들 경우 아래와 같이 형성이 가능하다.
    
    ```python
    from airflow import models
    from airflow.operators.python import PythonOperator
    
    def this_is_your_function():
        print("Hello world")
    
    def this_is_your_second_funtion():
        print("Second func")
        
    with models.DAG(
        "python-operator-2",
    ) as dag:
    
       python_component = PythonOperator(
           task_id="first_pythonoperator",
           python_callable=this_is_your_function
       )
       
       python_second = PythonOperator(
           task_id="second_task",
           python_callable=this_is_your_second_funtion
       )
       
       (
           python_component
           >> python_second
       )
    ```
    
- 여러 개의 컴포넌트를 생성한 뒤 순서에 맞게 연결만 해주면 된다.

## How to pass arguments?

- `PythonOperator` 에 매개변수는 어떻게 전달할까?
- `python_callable` 에 실행할 함수를 지정하지만, 같이 매개변수를 전달하고 싶을 수도 있다.
    
    ```python
    from airflow import models
    from airflow.operators.python import PythonOperator
    
    def argument_test_func(context):
        print(context)
     
    with models.DAG(
        "python-operator-3",
    ) as dag:
       
       argu_test = PythonOperator(
           task_id="argu-pass-test",
           python_callable=argument_test_func,
           op_kwargs={"context": "Hello world"},
       )
       
       (
           argu_test
       )
    ```
    
- `PythonOperator` 내 `op_kwargs` 를 이용해 key/value 형태로 전달이 가능하다.
- 실제 key 값인 `context` 는 `argument_test_func` 의 매개변수 인자 이름과 동일해야한다.

## How to get previous component return value?

- 이전 `PythonOperator` 컴포넌트에서 반환한 값을 어떻게 받을 수 있을까?
- 실제 파이프라인을 구성하다보면 이전 컴포넌트 값을 그대로 활용할 필요가 존재한다.
- Airflow의 `xcom` 타입을 이용해 간단하게 접근이 가능하다.
    
    ```python
    from airflow import models
    from airflow.operators.python import PythonOperator
    
    def first_func():
        li = [1, 2, 3, 4]
        return li
    
    def second_func(**context):
        li = context['task_instance'].xcom_pull(task_ids='first_job')
        
        for i in li:
            print(i)
        return
    
     
    with models.DAG(
        "python-operator-4",
    ) as dag:
        
        first_job = PythonOperator(
            task_id='first_job',
            python_callable=first_func
        )
        
        second_job = PythonOperator(
            task_id='second_job',
            python_callable=second_func
        )
        
        (
            first_job
            >> second_job
        )
    ```
    
- 먼저 `first_func` 에서 리스트를 반환하고, `second_func` 에서 하나씩 출력하는 예제이다.
- 컴포넌트 간의 데이터 전송 및 저장은 `task_id` 를 키 값으로 해서 진행된다는 것이다.
- 즉, `context['task_instance'].xcom_pull(task_ids='first_job')`