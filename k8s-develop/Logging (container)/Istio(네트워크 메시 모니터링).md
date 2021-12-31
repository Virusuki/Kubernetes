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
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.ya 
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







