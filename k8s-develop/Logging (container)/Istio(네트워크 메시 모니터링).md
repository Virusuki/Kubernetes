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
```
# 
```


