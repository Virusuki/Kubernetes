# Istio는 무엇인가?
- 다수의 컨테이너가 동작하는 경우에 각 컨테이너의 트래픽을 관찰하고 정상 동작하는지 모니터링하기가 어렵기 때문에 Devops팀에 부담
- 개발자는 이식성을 우해 마이크로서비스를 사용하여 아키텍처를 설계하고 운영자는 이 컨테이너들을 다양한 클러스터에 배포 및 관리
- 서비스 메시의 크기와 복잡성이 커짐에 따라 이해하고 관리하기가 어려워짐 (예: 로드밸런싱, 장애 복수, 메트릭 및 모니터링)
- Istio는 쿠버네티스 환경의 네트워크 mesh 이슈를 보다 간편하게 해결하기 위해 지원하는 환경

## Istio Install(설치)
- Istioctl 설치
- istioctl은 kubectl이 설치된 곳에 설치해야 함. (마스터노드에서 istioctl을 구성함)

```
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.12.1
export PATH=$PWD/bin:$PATH # 실행 경로를 환경 변수에 추가
istioctl # kubectl 설정을 사용
```

- istio 배포 형태 
- istiod 데몬 역할
- istio-ingressgateway는 들어오는 트래픽을 로드밸런싱해주는 기능(ALB-Application Load-balance + uri/domain), 
  L7계층에서 부하분산해주는 기능(쿠버의 ingress 대체)
- istio-egressgateway는 나가는 트래픽에 대해 분산해주는 역할
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Istio_deploy_design.PNG" width="600px" height="270px" title="px(픽셀) 크기 설정" alt="istio deploy 형태"></img><br/>   

## 쿠버네티스에 istio 구성하기
- istioctl을 사용해 데모 버전으로 설치

- istioctl 명령어 설치이며, install부터는 kuber에다가 istio를 구성할 수 있도록 설치, --set profile은 demo 버전(그림참조)으로 설치
```
# istioctl install --set profile=default --skip-confirmation
```

- 네임스페이스 레이블을 istio 인젝션이 수행되도록 수정
- istio를 적용하고 싶은 namespace에다가 레이블을 지정함. 
- 만약 default 네임스페이스에다가 구성할 경우, 네임스페이스에 레이블을 달고, default ns 배포되는 (구성되지않은)pod들은 자동으로 해당 ns에 들어감
- 추가 컨테이너는 proxy역할을 하고, 엠베서더 컨테이너로서 ingress/egress 트래픽들을 관찰할 수 있도록 추가 컨테이너가 배포되는 걸로 이해하면 됨
```
# kubectl label namespace default istio-injection=enabled
```

### istio 구동을 위한 애플리케이션 배포
- Book Info에 대한 프로젝트 배포

```
kubectl delete all --all # 잘못 설치한 경우 삭제
kubectl delete limitrange default-limit-range # 잘못 설치한 경우 삭제
kubectl delete -f samples/bookinfo/platform/kube/bookinfo.yaml # 잘못 설치한 경우 삭제
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```


- Book Info를 외부로 서비스할 수 있도록 게이트웨이 생성 (istio의 기능)

```
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```

- 그림에서, default 네임스페이스에 istio-injection=enabled 라는 레이블 달아놓으면, 배포가 되는 시점에 Python[Product page]측면에
  proxy가 붙여지고, 들어오는 request 및 나가는 트래픽을 관찰할 수 있음
- java로 배포하는 부분도 마찬가지로 proxy 컨테이너가 붙으면서 모든 트래픽의 모니터링이 가능함
- 여기서, 관찰하는 proxy 파드들이 생성되면서 클러스터의 부하가 발생하는 단점은 있지만, 장애에 대한 처리하는 속도는 굉장히 빨라짐
- proxy 파드는 작은 단위로 올라가기 때문에 부하가 크게 미치지는 않음
- bookinfo-gateway.yaml에 부하분산 rule이 있음, L7 ingress에 트래픽이 들어오게 되면 python으로 포워딩하는 기능이 설정되어 있음
- ingress 게이트웨이는 istioctl install --set profile=demo 설치를 통해 생성됨.
- 아래의 구조는 ratings, reviews, details 표현하는 앱들로 구성되어 있으며 MSA라는형태로 되

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/book_info_architecture.PNG" width="640px" height="330px" title="px(픽셀) 크기 설정" alt="istio deploy 형태"></img><br/>


- bookinfo-gateway.yaml 실행 후, get pod 보면 2개씩 pod가 배포됨
- pod가 2개씩 배포 된 것은 1개: 앱 컨테이너, 1개: proxy 컨테이너

```
root@namuk-01:~/istio/istio-1.12.1# kubectl get pod
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-5498c86cf5-wjkvw       2/2     Running   0          17m
productpage-v1-65b75f6885-ztq8b   2/2     Running   0          16m
ratings-v1-b477cf6cf-6sdm6        2/2     Running   0          17m
reviews-v1-79d546878f-jgsnj       2/2     Running   0          17m
reviews-v2-548c57f459-4x62t       2/2     Running   0          16m
reviews-v3-6dd79655b9-52vvg       2/2     Running   0          16m
```

- details-v1-5498c86cf5-wjkvw에서 proxy 컨테이너 확인
```
kubectl get pod -o yaml details-v1-5498c86cf5-wjkvw 
```

NET_ADMIN과 NET_RAW는 네트워크의 정보를 변경하기 위해 권한을 가져옴
```
 image: docker.io/istio/proxyv2:1.12.1
    imagePullPolicy: IfNotPresent
    name: istio-init
    resources:
      limits:
        cpu: "2"
        memory: 1Gi
      requests:
        cpu: 100m
        memory: 128Mi
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        add:
        - NET_ADMIN
        - NET_RAW
        drop:
        - ALL
      privileged: false
      readOnlyRootFilesystem: false
      runAsGroup: 0
      runAsNonRoot: false
      runAsUser: 0
```


- ingress gateway와 북인포 프로젝트 연결 수행함.
- 생성된 gateway에 의해 어떻게 로드밸런싱할지 결정

```
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml

kubectl get svc -n istio-system -l istio=ingressgateway
```

- samples/bookinfo/networking/bookinfo-gateway.yaml를 보면 80포트로 접속 밒 서비스쪽 bookinfo-gateway에 들어오면
  /productpage,/static 등 uri에 따라서 route destination(productpage)로 구성 및 9080으로 보내지도록 룰이 구성되어 있음
```
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
  - "*"
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        exact: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: productpage
        port:
          number: 9080
```

- kubectl get svc 확인
- productpage 서비스가 앞단의 python 페이지라고 보면 됨

```
root@namuk-01:~/istio/istio-1.12.1# kubectl get svc
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.110.46.201    <none>        9080/TCP   33m
kubernetes    ClusterIP   10.96.0.1        <none>        443/TCP    149m
productpage   ClusterIP   10.104.153.117   <none>        9080/TCP   33m
ratings       ClusterIP   10.106.244.189   <none>        9080/TCP   33m
reviews       ClusterIP   10.97.62.190     <none>        9080/TCP   33m
```

- 80페이지로 접속하면 productpage로 연결

```
kubectl get svc -n istio-system -l istio=ingressgateway
 or
root@namuk-01:~/istio/istio-1.12.1# kubectl get svc -n istio-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.101.193.63   <pending>     15021:31254/TCP,80:30376/TCP,443:31328/TCP   152m
```

- 대시보드와 데이터베이스를 설치하고 서비스 오픈
```
kubectl apply -f samples/addons/kiali.yaml
kubectl apply -f samples/addons/prometheus.yaml

배포될때까지 기다렸다가  
@ kubectl get svc -n istio-system에서 kiali 서비스가 clusterIP로 있는데 kiali 서비스를 NodePort로 수정해야함
istioctl dashboard kiali # localhost:20001 서비스를 오픈
```

- kiari 접속 대시보드   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/kiari_dashboard.PNG" width="680px" height="400px" title="px(픽셀) 크기 설정" alt="kiari dashboard"></img><br/>


- product webpage 접속량에 따른 kiari 대시보드 모니터링
- kiari dashboard - Graph의 namespace 포함    

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/kiari_connection_confirm.PNG" width="950px" height="400px" title="px(픽셀) 크기 설정" alt="kiari conne"></img><br/>


- uri에 정의되지 않은 /test로 페이지를 찾을 수 없음 상태를 kiari 대시보드에서 unknown 확인
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/error_status_confirm.PNG" width="950px" height="400px" title="px(픽셀) 크기 설정" alt="kiari conne"></img><br/>



- 생성한 istio 관련 패키지 및 프로젝트 삭제
```
kubectl label ns default istio-injection-
kubectl delete all --all --force
kubectl delete ns --force istio-system
```
