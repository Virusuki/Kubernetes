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





## GitOps 적용
- 
[Example GitOps Pipeline]


## GitOps의 구현체(ArgoCD)
-
[ArgoCD 셋업 과정]



Reference:
- https://www.weave.works/technologies/gitops/
- 

