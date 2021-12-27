# 쿠버네티란? 
- 쿠버네티스는 컨테이너화된 워크로드와 서비스를 관리하기 위해 이식성이 있고, 확장가능한 오픈소스 플랫폼이다. 
- 쿠버네티스는 선언적 구성과 자동화를 모두 용이하게 해주며, 크고, 빠르게 성장하는 생태계를 가지고 있습니다.
  - 쿠버네티스는 K8s라는 표기로 "K"와 "s" 그 사이에 있는 8글자를 나타내는 약식 표기로도 사용한다.

[kubernetes 공식문서]

## 쿠버네티스 디자인 원칙
- 쿠버네티스 클러스터의 설계는 3가지 원칙에 기반을 두고 있습니다.
1. 보안성
2. 사용자 편의성
3. 확장 가능성


### 쿠버네티스 구조
> Control plane

- 컨트롤 플레인
    1. kube-apiserver: 쿠버네티스 API를 노출, 쿠버네티스 control plane의 프론트엔드, 수평으로 확장 가능
       - 즉, 더 많은 인스턴스를 배포해서 확장할 수 있습니다.
    2. etcd: 모든 클러스터 데이터를 담는 쿠버네티스 뒷단의 저장소로 사용되는 일관성, 고가용성, 키-값 저장소
    3. Kube-scheduler: 노드가 배정되지 않은 새로 생성된 파드를 감지하고, 실행할 노드를 선택
    4. kube-controller-manager: ReplicaSets, Deployment, Service와 관련한 제어 루프를 수행
       - 노드가 다운되면 통지/대응, 알맞은 수의 파드 유지, Service와 파드 연결 등
    

- 컨트롤 플레인
    1. kube-api

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Kubernetes%20Architecture/files/img/kubernetes-architecture.PNG" width="850px" height="680px" title="px(픽셀) 크기 설정" alt="Kubernetes Structure"></img><br/>

### 용어
- 워크로드: 시스템에 의해 실행되어야할 작업의 할당량 

### Reference:
- https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/
- https://kubernetes.io/docs/reference/setup-tools/kubeadm/implementation-details/
- https://www.redhat.com/ko/topics/containers/kubernetes-architecture
