# Airflow with GCP pub/sub

### How to get pub/sub message in Airflow?

- 스트리밍 데이터를 처리할 경우 메시징 큐 시스템을 필수적으로 사용한다.
- 이번 문서에서는 GCP pub/sub 메시지를 Airflow 환경에서 가져와 처리하는 방법에 대해 정리하였다.

### 사전 작업

- 먼저 구글 클라우드 플랫폼에서 사전 진행해야할 작업들이 존재한다.
    - pub/sub topic 생성 + default subscriber 설정
        - topic 이름을 `test` 로 가정
        - project_id를 `gcp_test_project` 로 가정
        - default subscriber 이름을 `test-sub` 으로 가정
    - Cloud composer environment 생성

### DAG 생성

- Cloud composer는 Airflow 환경에서 DAG에 따라 파이프라인이 작동한다.
- Cloud composer를 활용하게 되면 GCP 각 서비스에 대한 **crendential 혹은 auth 부분을 생각하지 않아도 된다.**
- 따라서, 흐름에 맞는 DAG를 상황에 맞게 작성할 줄 알아야 한다.
    
    ```python
    import datetime
    import base64
    
    from airflow import models
    from airflow.operators.python import PythonOperator
    from airflow.providers.google.cloud.sensors.pubsub import PubSubPullSensor
    
    def print_msg(**context):
    		# task_instance에 접근해 xcom 타입으로 데이터에 접근할 수 있다.
    		pub_sub_job_return_val = context['task_instance'].xcom_pull(task_ids='pull_msg')
    	
    		for message in pub_sub_job_return_val: # 최대 50개의 메시지가 함께 전달된다.
    				
    				# pub/sub 메시지는 자동적으로 base64으로 인코딩 되므로, 다시 디코딩 과정이 필요하다.
    				decoded_data = base64.b64decode(message['message']['data']).decode('utf-8-sig')
    				print(decoded_data)
    
    if __name__ == "__main__":
        with models.DAG(
            'pub-sub-test-realtime',
            schedule_interval=datetime.timedelta(seconds=15), # 15초마다 실행한다.
            start_date=datetime.datetime(2022, 11, 1), # 시작시간은 22년 11월 1일 오전 12시부터 시작한다.
        ) as dag:
            
            pull_msg_job = PubSubPullSensor(
                task_id="pull_msg",
                ack_messages=True,
                project_id="gcp_test_project",
                subscription="test-sub",
                max_messages=50, # 최대 50개 메시지만 가져온다.
            )
    
    				print_msg_job = PythonOperator(task_id="print_pub_sub_msg", python_callable=print_msg)
    				
    				(
                pull_msg
                >> youtube_api_result
            )
    ```
    
- 위 코드에서 중요한 것은, pub/sub 메시지는 Airflow의 `PubSubPullSensor` 으로 Pull 한다는 것이며,
- 다른 컴포넌트에서 메시지들의 batch를 처리할 경우, `xcom` 타입으로 가져와 적절한 decoding이 필수적인 것이다.