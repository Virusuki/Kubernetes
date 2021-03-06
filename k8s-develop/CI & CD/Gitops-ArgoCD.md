## Devops (Development+Operation) 
 소프트웨어의 개발과 운영의 합성어로서, 소프트웨어 개발자와 정보기술 전문가 간의 소통, 협업 및 통합을 
 강조하는 개발 환경이나 문화를 말합니다. (위키백과)

 개인적으로 devops가 필요하고, 떠오르는 이유 중 큰 목표, 원칙, 협업 등 관련 문화를 공유할 뿐 상세한 규칙이나 절차는 비어있다는 점입니다.
 다시 말해서, 소프트웨어 개발/서비스 배포의 유연성과 확장성에 포커싱되어 있다고 생각합니다. 빠르게 발전/성장하는 시장에서 적합하고 살아남을
 수 있는 원동력이 되었고, 지금도 다양한 방식의 Gitops, Devsecops, MLOps 등 다양한 모델의 명칭으로 진화해나가고 있습니다.
 
 앞서, 언급한 방식 중 Gitops는 인프라 보안을 개선하고 가시성이 높일 수 있는 효율적인 배포 서비스로 공감하였고, 최근에 직접 적용해 본 'Gitops'에 대해
 설명하고자 합니다. Gitops의 기본 원칙과 패턴을 알아본 다음 구현체 중 하나인 ArgoCD를 활용해서 샘플 프로젝트에 적용하는 과정을 다루어 보고자 합니다.
 
 
## What is Gitops?
- Gitops는 Weaveworks라는 회사에서 처음 사용한 용어이며, 클라우드 네이티브 application을 타겟으로 한 지속적배포(Continuous Deployment)에 초점을 두고 있습니다.


## 단일 진실 공급원 (Single Source of Truth, SSOT)   
- 단일 진실 공급원은 하나의 결과(진실)가 하나의 이유(원천)에서 비롯된다라는 의미를 뜻합니다.
- Gitops에서 단일 진실 공급원은 git을 단일 진실 공급원을 지정하고, 이를 배포 형태를 선언 및 관리하는 것을 뜻합니다. 
- 오직 지정한 원천에서만 결과가 나오므로, 배포 상태를 확인하기 위해 원천 하나만 보면 되기 때문에 문제 해결에도 쉽게 파악할 수 있습니다.
- 안정적으로 운영환경에 배포할 수 있으므로, 사람의 손에 거치치 않아 human error를 최소화할 수 있는 장점이 있습니다.

### Example Gitops Pipeline
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/gitops-pipeline.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="gitops 파이프라인"></img><br/>

## Gitops의 모델
1. 선언형 모델 : The entire system described declaratively.
- Gitops에서 배포 방법은 command(명령형) 방식이 아닌 declaratively(선언형)으로 정의되어야 합니다.
- 예) 사용자가 kubectl run --image ,,, or kubectl -f example.yaml 와 같이 선언형으로 생성할 수 있습니다.
- git repository에서 버전이 지정된 애플리케이션 선언을 사용하면 SSOT(Single source of truth, SSOT) 조건을 만족할 수 있습니다.
- 그러면, app을 쉽게 배포하고 문제 발생시, 롤백하기도 쉽습니다.

2. Git을 이용한 버전 관리 : The canonical desired system state versioned in Git.
- 선언형 배포 관리 방식을 정의하며, Git을 통해 형상관리할 수 있습니다.
- 각 버전이 git 저장소에 기록되어 있어야 합니다.
- Git revert를 통한 쉽게 과거의 버전으로 롤백할 수 있습니다.

3. 변경사항에 자동화 반영 : Approved changes that can be automatically applied to the system.
- Git repository에 선언형 배포 정의서를 저장하면 배포가 일어나는 작업은 시스템에 자동으로 허용해야 합니다.
- 이것은 책임 주체가 ArgoCD와 같은 배포 주체가 되며, Gitops에는 상태 정의가 외부에 존재하는 분리된 환경이 됩니다.
- 이를 통해 human error를 줄이고 지속적 빌드 및 배포를 가능하게 할 수 있습니다.

4. 자가 치유 및 이상 탐지
- 사용자는 시스템의 배포 상태를 작성하게 되면 실제 배포 환경이 일치하고 있는지 ArgoCD와 같은 소프트웨어 에이전트가 배포 주체가 됩니다.
- 에이전트를 사용하면 현재 배포상태를 확인하고 Git repository의 변경 사항 등 체크하여 배포된 리소스가 이상 유무까지 확인합니다.



## GitOps의 구현체(ArgoCD)
- ArgoCD는 쿠버네티스의 오픈소스 도구이며, workflow를 실행 및 클러스터를 관리하고, Gitops의 배포를 수행합니다.
- 즉, ArgoCD에서 설정 사항을 Git에 푸시하면, 쿠버네티스의 클러스터 상태가 자동으로 Git에 정의된 상태로 동기화 됩니다.

## ArgoCD Install

``` 
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
``` 

- 쿠버네티스에서 ArgoCD pod 및 service가 잘 배포됐는지 확인
``` 
root@ubuntu-kube-master1:~# kubectl get svc,pod -n argocd
NAME                            TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/argocd-dex-server       ClusterIP      10.111.218.192   <none>        5556/TCP,5557/TCP,5558/TCP   3d23h
service/argocd-metrics          ClusterIP      10.96.140.178    <none>        8082/TCP                     3d23h
service/argocd-redis            ClusterIP      10.106.178.18    <none>        6379/TCP                     3d23h
service/argocd-repo-server      ClusterIP      10.102.184.241   <none>        8081/TCP,8084/TCP            3d23h
service/argocd-server           LoadBalancer   10.96.16.254     <pending>     80:31923/TCP,443:30460/TCP   3d23h
service/argocd-server-metrics   ClusterIP      10.97.67.122     <none>        8083/TCP                     3d23h

NAME                                      READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0       1/1     Running   0          3d23h
pod/argocd-dex-server-76c978c87-bd8js     1/1     Running   0          3d23h
pod/argocd-redis-5b6967fdfc-f2sm4         1/1     Running   0          3d23h
pod/argocd-repo-server-8555f94d4f-kfvrh   1/1     Running   0          3d23h
pod/argocd-server-bc59fd78c-ndvqv         1/1     Running   0          3d23h
``` 

- LoadBalancer로 변경 및 service의 IP를 확인
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc argocd-server -n argocd
```

- 쿠버네티스에서 변경된 service 확인
```
root@ubuntu-kube-master1:~# kubectl get svc argocd-server -n argocd
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
argocd-server   LoadBalancer   10.96.16.254   <pending>     80:31923/TCP,443:30460/TCP   3d23h
```

- ArgoCD 접속을 위한 ID 및 Password 확인 (secret 형태로 구성)
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

- service에서 할당 받은 IP로 https로 접속 및 https://192.168.100.186:30460/ 그리고, secret으로 확인된 password를 입력해서 로그인한다.
  - Username: admin
  - Password: [쿠버네티스 secret으로 할당받은 값]
  
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Argocd_%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%99%94%EB%A9%B4.PNG" width="700px" height="500px" title="px(픽셀) 크기 설정" alt="ArgoCD login"></img><br/>

<br>

- ArgoCD에서 빨간색 표시된 NEW APP 버튼 클릭해서 App을 추가
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/ArgoCD_newapp.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="ArgoCD Newapp"></img><br/>
<br>

- Application Name은 Jupyterlab-container 및 Project는 default로 설정합니다. SYNC POLICY는 자동/수동 선택합니다. 자동 동기화는 Git에 설정된 manifest 내용과 현재 application
  상태가 다르거나 정상동작하지 않으면 동시화해서 다시 배포 및 재구성이 가능하다. 수동으로 배포를 진행 및 namespace를 생성할 수 있도록 sync option에 선택합니다.

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/ArgoCD_General_conf.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="ArgoCD General"></img><br/>


- Github Repository의 app 파일 위치를 지정합니다. Repository URL에 https://github.com/Virusuki/ArgoCD-Gitops 로 설정 및 main 브랜치를 사용하였습니다. path는 배포할 app의 디렉토리를 지정합니다. (지정한 디렉토리안에 deployment.yaml와 service.yaml이 포함되어 있습니다.
- Cluster URL은 쿠버네티스 내에 argo가 구성되어 있어서 kube-apiserver에 대한 URL 정보를 도메인 주소로 입력합니다. 그 다음에, namespace명을 설정하고 create 버튼을 누릅니다.

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/ArgoCD_Source_des.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="ArgoCD Source"></img><br/>


- ArgoCD에서 Jupyterlab-container App이 생성된 것을 확인할 수 있습니다.
- SYNC 버튼을 눌러서 deploy를 진행할 수 있습니다.
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/ArgoCD_App.PNG" width="750px" height="500px" title="px(픽셀) 크기 설정" alt="ArgoCD Deploy-App"></img><br/>
<br>

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/ArgoCD_sync_click.PNG" width="750px" height="500px" title="px(픽셀) 크기 설정" alt="ArgoCD App snyc"></img><br/>

- 쿠버네티스에서 Application이 정상적으로 배포됐는지 확인할 수 있습니다.
```
root@ubuntu-kube-master1:~# kubectl get pod,deployments.apps,svc -n jupyter-ns
NAME                               READY   STATUS    RESTARTS   AGE
pod/jupyter-user-6759d5899-pzhmm   1/1     Running   0          3d21h
pod/jupyter-user-6759d5899-tllk7   1/1     Running   0          3d21h
pod/jupyter-user-6759d5899-xhz54   1/1     Running   0          3d21h

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/jupyter-user   3/3     3            3           3d21h

NAME                           TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/service-jupyter-user   NodePort   10.103.107.47   <none>        9000:30895/TCP   3d21h
```

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/ArgoCD_Deploy_App.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="ArgoCD App deploy"></img><br/>



Reference:
- https://www.weave.works/technologies/gitops/
- https://argo-cd.readthedocs.io/en/stable/

