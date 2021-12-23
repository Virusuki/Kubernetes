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


## Gitops의 모델
1. 선언형 모델 (Declarative Model)   
- 


2. 단일 진실 공급원 (Single Source of Truth, SSOT)   
- 


## GitOps 적용
- 
[Example GitOps Pipeline]


## GitOps의 구현체(ArgoCD)
-
[ArgoCD 셋업 과정]

