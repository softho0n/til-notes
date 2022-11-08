# Airflow SlackOperator

- Airflow DAG에서 특정 컴포넌트 혹은 전체 DAG의 실행이 완료되었을 시점에 Slack 알림을 보낼 수 있다.
- Airflow에 존재하는 `SlackOperator` 를 사용하여 실제 알림 봇을 만드는 방법에 대해 정리하였다.
- 먼저 `apache-airflow-providers-slack` 을 설치해야한다.
    
    ```bash
    pip install apache-airflow-providers-slack
    ```
    

## Slack App 생성

- 위 작업을 진행하기 위해서 먼저 Slack App을 사전에 정의해야한다.
- 자세한 내용은 해당 링크를 참고하면 좋다. ([https://miaow-miaow.tistory.com/148](https://miaow-miaow.tistory.com/148))
- 순서는 아래와 같다.
    1. Slack App 및 Workspace 생성
    2. Bot 유저 및 Token 생성
    3. Bot Scope 설정 → 메시지 전송 권한 부여
    4. OAuth Token을 App에 추가
    5. OAuth Token 복사 후 따로 저장
    6. Slack 워크스테이션에 생성한 App 추가
    7. 테스트하기 위한 채널에 해당 앱 초대

## Airflow Slack Connection ID 생성

- 위에서 생성한 OAuth Token 값을 가지고 있을 것이다.
- Airflow에서 SlackOperator는 OAuth Token을 기반으로 API 호출을 진행한다.
- SlackOperator를 정상적으로 사용하기 위해 새로운 Connection ID를 만들 필요가 있다.
- 먼저, **Airflow UI에 접속한 후 Admin > Connections 페이지로 이동한다.**
- 그 다음, 새로운 Connection **생성을 위한 버튼을 클릭**한다.
- Connection ID에는 간단하게 **slack**으로 기입하고, **Connection Type을 Slack API로 설정**한다.
- 그리고, **Slack API Token 필드에 기록해두었던 OAuth Token 값을 기입**해준다.
- **Test 버튼**을 통해 정상적으로 API 호출이 되는지 확인한 뒤, 저장한다.

## SlackOperator 사용 예제

- 이제 본질적인 `SlackOperator` 사용을 위한 사전 준비는 끝났다.
- 아래는 간단한 알림 예제이다.
- success 인 경우와 failure 인 경우에 대해 알림을 보내는 클래스를 아래와 같이 구성한다.
    
    ```python
    # slack.py
    from airflow.hooks.base import BaseHook
    from airflow.providers.slack.operators.slack import SlackAPIPostOperator
    
    class SlackAlert:
        def __init__(self, channel):
            self.slack_channel = channel
            # connection을 위의 단계에서 slack이란 이름으로 생성했음.
            # BaseHook 클래스의 password 멤버변수에 해당 토큰 값이 암호화되어있으며, 접근이 가능함.
            self.slack_token = BaseHook.get_connection('slack').password
            
        def slack_failure_alert(self, context):
            alert = SlackAPIPostOperator(
                task_id='slack_failed',
                channel=self.slack_channel,
                token=self.slack_token,
                text="""
                    *Result* Failed :alert:
                    *Task*: {task}  
                    *Dag*: {dag}
                    *Execution Time*: {exec_date}  
                    *Log Url*: {log_url}
                    """.format(
                        task=context.get('task_instance').task_id,
                        dag=context.get('task_instance').dag_id,
                        exec_date=context.get('execution_date'),
                        log_url=context.get('task_instance').log_url,
                        )
                      )
            return alert.execute(context=context)
    
        def slack_success_alert(self, context):
            alert = SlackAPIPostOperator(
                task_id='slack_success',
                channel=self.slack_channel,
                token=self.slack_token,
                text="""
                    *Result* Success :checkered_flag:
                    *Task*: {task}
                    *Dag*: {dag}
                    *Execution Time*: {exec_date}
                    *Log Url*: {log_url}
                    """.format(
                        task=context.get('task_instance').task_id,
                        dag=context.get('task_instance').dag_id,
                        exec_date=context.get('execution_date'),
                        log_url=context.get('task_instance').log_url,
                        )
                      )
            return alert.execute(context=context)
    ```
    
    → `slack_success_alert` 함수의 경우 dag 혹은 component 작업이 정상적으로 수행되었을 때 수행됨.
    
    → `slack_failure_alert` 함수의 경우 dag 혹은 component 작업이 실패했을 경우에 수행됨.
    
- 전체 코드의 예시는 아래와 같음.
    
    ```python
    # dag.py
    import datetime
    import pendulum
    
    from airflow import models
    from airflow.hooks.base import BaseHook
    from airflow.providers.slack.operators.slack import SlackAPIPostOperator
    from airflow.operators.python import PythonOperator
    
    from slack import SlackAlert
    
    def print_hello(**context):
        print("Hello world")
    
    with models.DAG(
        "slack-noti-test",
        schedule_interval=None,
        start_date=datetime.datetime(
            2022, 11, 8, 18, 0, tzinfo=pendulum.timezone("Asia/Seoul")
        ),
    ) as dag:
        
        # 채널명을 #와 함께 붙혀 기입해준다.
        alert = SlackAlert("#test")
        
        simple_component = PythonOperator(
            task_id="slack_test_func",
            python_callable=print_hello,
            on_success_callback=alert.slack_success_alert,
            on_failure_callback=alert.slack_failure_alert,
        )
        
        (
            simple_component
        )
    ```
    
    → `simple_component` 를 수행하면서 콜백 함수로 `SlackAlert` 의 함수를 등록할 수 있다.