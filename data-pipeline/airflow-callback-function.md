# Airflow Callback Functions

- Airflow에서 특정 Operator가 성공적으로 수행되었을 때 or 실패했을 때 따로 처리가 필요할 것이다.
- Airflow에서는 `on_success_callback` 과 `on_failure_callback` 함수를 지원한다.
- `on_success_callback` 은 Operator 및 Dag 가 성공적으로 수행되었을 때 실행되는 함수이다.
- 반대로, `on_failure_callback` 함수는 실패했을 때 실행되는 함수이다.
- 즉, 성공과 실패의 경우에 따라 예외 처리를 할 때 사용하기 적절하다고 볼 수 있다.

## Example

- 실제 사용 예제는 아래와 같다.
    
    ```python
    from airflow import models
    from airflow.operators.python import PythonOperator
        
    def on_success_callback_func(context):
        task_id = context['task_instance'].task_id
        dag_id  = context['task_instance'].dag_id
        
        print(f"{dag_id}_{task_id} success")
    
    def on_failure_callback_func(context):
        task_id = context['task_instance'].task_id
        dag_id  = context['task_instance'].dag_id
        
        print(f"{dag_id}_{task_id} failure")
    
    def test():
        print("Hello world")
        
    with models.DAG(
        'callback-pipeline',
    ) as dag:
        
        callback_test_job = PythonOperator(
           task_id="callback_test_job",
           python_callable=test,
           on_success_callback=on_success_callback_func,
           on_failure_callback=on_failure_callback_func,
        )
        
        (
            callback_test_job
        )
    ```
    
- `callback_test_job` 은 `test` 함수를 실제로 수행하게 된다.
- 해당 job이 성공적으로 수행되었다면 `on_success_callback_func` 이 수행될 것이고,
- 해당 job이 실패했다면 `on_failure_callback_func` 이 수행될 것이다.