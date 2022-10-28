# How to install kubeflow?

### Kubeflow란?

- 쿠버네티스 환경 상에서 작동하는 MLOps 시스템 중 하나이다.
- MLOps 오케스트레이션 작업을 담당한다.
- Model Serving, Katib, Pipeline, Notebooks 등의 기능을 제공하며 핵심 컴포넌트는 파이프라인이다.

### How to install `Kubeflow` ?

- Kubeflow를 설치하기 위해서 사전 진행되어야 하는 작업이 존재한다.
    - 이미 docker 런타임은 정상적으로 설치되었다고 가정.
    - 도커 런타임을 기반으로 k8s 클러스터 구축이 완료 되었다고 가정.
- Kubeflow 설치를 위해선 `Kustomize` 라는 도구가 필요하다.
    - Kustomize란 쿠버네티스 패키지 구성을 관리하고 정의하는 도구이다.
    - 즉, 쉽게 리소스의 집합을 구성할 수 있으며 구성된 리소스들의 집합을 편리하게 설치할 수 있다.
    - Kubeflow 설치를 위해선 Kustomize 3.2.0 버전을 권장하고 있다.
        
        ```bash
        wget https://github.com/kubernetes-sigs/kustomize/releases/download/v3.2.0/kustomize_3.2.0_linux_amd64
        chmod +x kustomize_3.2.0_linux_amd64
        sudo mv kustomize_3.2.0_linux_amd64 /usr/local/bin/kustomize
        kustomize version
        
        # Version: {KustomizeVersion:3.2.0 GitCommit:a3103f1e62ddb5b696daa3fd359bb6f2e8333b49 BuildDate:2019-09-18T16:26:36Z GoOs:linux GoArch:amd64}
        ```
        
- Kubeflow 관련 리소스 집합들은 [https://github.com/kubeflow/manifests](https://github.com/kubeflow/manifests) 에 정의되어 있다.
    - 해당 매니페스트를 다운로드 한 뒤, kustomize를 통해 정상적으로 설치를 진행한다.
    - 설치 과정에서 다양한 네임 스페이스가 같이 생성된다.
        
        ```bash
        $ git clone https://github.com/kubeflow/manifests
        $ cd ./manifests
        $ while ! kustomize build example | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done
        
        # 모든 Pods가 Running 상태가 되어야 함.
        $ kubectl get pods -n cert-manager
        $ kubectl get pods -n istio-system
        $ kubectl get pods -n auth
        $ kubectl get pods -n knative-eventing
        $ kubectl get pods -n knative-serving
        $ kubectl get pods -n kubeflow
        
        NAME                                                     READY   STATUS    RESTARTS   AGE
        admission-webhook-deployment-c77d48bbb-twxdg             1/1     Running   0          2d8h
        cache-server-56d94f5d78-d8cz5                            2/2     Running   0          2d8h
        centraldashboard-6b9ccd7788-6snf5                        2/2     Running   0          2d8h
        jupyter-web-app-deployment-5bc998bcb5-srclm              1/1     Running   0          2d8h
        katib-controller-6848d4dd9f-nsz9h                        1/1     Running   0          2d8h
        katib-db-manager-665954948-cdjt4                         1/1     Running   0          2d8h
        katib-mysql-5bf95ddfcc-r92bv                             1/1     Running   0          2d8h
        katib-ui-56ccff658f-pqnvj                                1/1     Running   0          2d8h
        kserve-controller-manager-0                              2/2     Running   0          2d8h
        kserve-models-web-app-5878544ffd-cjw7j                   2/2     Running   0          2d8h
        kubeflow-pipelines-profile-controller-5d98fd7b4f-j8rkh   1/1     Running   0          2d8h
        metacontroller-0                                         1/1     Running   0          2d8h
        metadata-envoy-deployment-5b685dfb7f-6mddq               1/1     Running   0          2d8h
        metadata-grpc-deployment-f8d68f687-jf4n9                 2/2     Running   1          2d8h
        metadata-writer-d6498d6b4-q2g2v                          2/2     Running   0          2d8h
        minio-5b65df66c9-jgn9m                                   2/2     Running   0          2d8h
        ml-pipeline-844c786c48-468pp                             2/2     Running   4          2d8h
        ml-pipeline-persistenceagent-5854f86f8b-jd48n            2/2     Running   0          2d8h
        ml-pipeline-scheduledworkflow-5dddbf664f-pmpxd           2/2     Running   0          2d8h
        ml-pipeline-ui-6bdfc6dbcd-mtv8f                          2/2     Running   0          2d8h
        ml-pipeline-viewer-crd-85f6fd557b-5c7cl                  2/2     Running   1          2d8h
        ml-pipeline-visualizationserver-7c4885999-hmfq4          2/2     Running   0          2d8h
        mysql-5c7f79f986-79vzp                                   2/2     Running   0          2d8h
        notebook-controller-deployment-6478d4858c-r6tzv          2/2     Running   1          2d8h
        profiles-deployment-7bc47446fb-tqclx                     3/3     Running   1          2d8h
        tensorboard-controller-deployment-f4f555b95-9d7k7        3/3     Running   1          2d8h
        tensorboards-web-app-deployment-7578c885f7-dv2jr         1/1     Running   0          2d8h
        training-operator-7cd4b49f76-j7k59                       1/1     Running   0          2d8h
        volumes-web-app-deployment-7bc5754bd4-pnnl2              1/1     Running   0          2d8h
        workflow-controller-6b9b6c5b46-8c5jh                     2/2     Running   1          2d8h
        ```
        
    - 위 명령어를 정상적으로 실행시켰다면 모든 Pod가 Running 상태에 도달하게 될 것이다.
    - 최대 15분까지 시간이 소요될 수 있으므로 지속적인 모니터링이 필요하다.
    

### Kubeflow Dashboard

- Kubeflow에서는 관련 서비스를 접근할 수 있게 Dashboard 또한 제공한다.
- Dashboard에 접근하여 Notebook을 생성할 수도 있으며, 파이프라인 업로드, 모델 서빙 등의 고수준 레벨에서의 작업 수행이 가능하다.
- 처음 kubeflow 설치 시에 기본으로 생성된 계정 정보는 다음과 같다.
    - `ID : user@example.com`
    - `PW : 12341234`
- 실제로 접근하기 위해선 `istio-system` 네임스페이스에 정의된 `istio-ingressgateway` 서비스를 통해 접근 할 수 있다.
    
    ```bash
    kubectl get svc -n istio-system
    
    NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                      AGE
    authservice             ClusterIP      10.110.63.181    <none>        8080/TCP                                                                     2d8h
    cluster-local-gateway   ClusterIP      10.103.219.60    <none>        15020/TCP,80/TCP                                                             2d8h
    istio-ingressgateway    LoadBalancer   10.96.20.253     <pending>     15021:30662/TCP,80:30648/TCP,443:31236/TCP,31400:32092/TCP,15443:31748/TCP   2d8h
    istiod                  ClusterIP      10.105.179.147   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP                                        2d8h
    knative-local-gateway   ClusterIP      10.98.173.8      <none>        80/TCP                                                                       2d8h
    ```
    
- 클러스터 내부의 80번 포트와 외부 0.0.0.0 호스트의 30648번 포트가 포워딩 되어있음을 알 수 있다.
- 따라서, 클러스터 host:30648로 외부에서 접속을 시도하면 정상적으로 Dashboard에 접근할 수 있을 것이다.