## Background
클라우드 네이티브에서 점점 더 중요한 역할을 하고 있으므로, 신속한 확장을 요하는 클라우드 네이티브 애플리케이션을 호스팅하는데 이상적인 플랫폼이다.
K8S라고도 하며, Kubernetes는 컨테이너화된 application의 배포, 확장 및 관리하는 서비스 중심의 아키텍처로 변화되면서 컨테이너를 오케스트레이션하는 
쿠버네티스의 중요성이 높아졌습니다. MicroService와 데브옵스(Devops) 환경에서 application을 빠르게 개발 및 배포하는 애자일 방법론을 적용하기 위해
쿠버네티스와 다른 요소 간의 기술을 제공하는 클라우트 네이티브 오픈소스 S/W 필수적이며, 클라우드 제품이 실행되는 컨테이너가 기준이 되었습니다.

### Who is it for?
해당 CKA 인증은 쿠버네티스 관리자, 클라우드 관리자 및 쿠버네티스 인스턴스를 관리 및 기타 IT 전문가로 생각해볼수가 있습니다.

## CKA을 위한 요구되는 지식
- 쿠버네티스 리소스: 워크로드 오브젝트
1. POD
2. CNI 컨테이너 네트워크 인터페이스 (컨테이너 런타임)

- 쿠버네티스 리소스: 컨트롤러 오브젝트
1. ReplicaSet
2. Deployment
3. DaemonSet
4. Service (로드밸런서 오브젝트)
5. Ingress (로드밸런서 오브젝트)
6. PV, PVC (스토리지 오브젝트)

- APIs / Access [접근/권한]
1. Label과 Annotation
2. API접근
3. 인증서
4. RBAC (Role Based Access Control) - Role, ClusterRole / RoleBinding, ClusterRoleBinding, ServiceAccount

- Managing State with Deployment, Scheduling [Compute]
1. Policy 정책 - (Node Selector, Node Affinity, Taint/Toleration)
2. Multi Container Pod
3. Namespace
4. Label과 Selector
5. Static pod
6. 클러스터 노드 작업 (cordon/uncordon, drain, taint)
7. 클러스터 업그레이드

- Volumes and data [Storage]
1. Persistent Volume(PV) - 관리자가 프로비저닝 or 스토리지 클래스를 사용하여 동적으로 프로비저닝한 클러스터의 스토리지
2. Persistent Volume Claim(PVC) - 사용자의 스토리지에 대한 요청, 파드와 비슷
3. ConfigMap - key/value 쌍으로 기밀이 아닌 데이터를 저장하는데 사용하는 API 오브젝트

- Services and Ingress [Network]
1. Services (L4 영역)
  √ 여러 레플리카에 트래픽을 분산시키는 로드밸런서 (TCP, UDP 모두 가능)
  √ 노드의 kube-proxy를 활용한 endpoint로 라우팅 되도록 처리
2. DNS (Domain Name System)은 호스트의 도메인 이름을 호스트의 네트워크 주소로 바꾸거나 그 반대의 변환을 수행할 수 있도록 하기 위해 개발됨. (위키백과)
  √ 기본 값인 CoreDNS를 애드온
3. Ingress (L7 영역)
  √ 클러스터 내의 서비스에 대한 외부 접근을 관리하는 API오브젝트이며, HTTP를 관리
4. Networkpolicy 정책 - Label을 사용하여 pod/namespace를 지정 및 pod/namespace로 유입되는 모든 네트워크 트래픽을 구성

- Logging and Troubleshooting, Backup, Upgrade [Operation]
1. SRE(Site Reliability Engineering) - 구글의 사이트 신뢰성 엔지니어링 [참고사항]
  √ SRE들은 Devops를 구현 : DevOps는 IT의 silo, ops, network, securiy 등을 허무는 방식, 가이드라인, 문화의 집합
2. 메트릭&모니터링 / 용량 관리 / 변화 관리 / 긴급대응 / 문화
3. 메트릭/로킹(센싱) -> 메트릭/로깅/트레이스(분석) -> 트러블슈팅
4. 쿠버네티스의 logs 및 top, Side-car
5. 백업 및 복구
  √ ETCDCTL_API=3 etcdctl --endpoints, --cacert, --cert, --key snapshot save /tmp/snapshot-pre-boot.db
  √ ETCDCTL_API=3 etcdctl --endpoints, --cacert, --cert, --key snapshot snapshot restore /tmp/snapshot-pre-boot.db
  



## CKA 후기

<img src="https://github.com/Virusuki/kubernetes-k8s/blob/main/CKA/Namuk%20Kim-CKA.PNG" width="650px" height="500px" title="px(픽셀) 크기 설정" alt="CKA자격증"></img><br/>
