# :rocket: Kubernetes Tech


- ### Kubernetes Architecture
  - [쿠버네티스 구조/개념](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Kubernetes%20Architecture/Kubernetes%20%EA%B0%9C%EB%85%90%EA%B3%BC%20%EA%B5%AC%EC%A1%B0.md)
  
  - [쿠버네티스 설치[우분투용]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Kubernetes%20Architecture/Kubernetes%20Install%20(ubuntu).md)

- ### Pod container Design
  - [Multi Container [멀티 컨테이너]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Pod-Container%20Design/Multi-container.md)
  - [Liveness Probe [라이브네스 프로브]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Pod-Container%20Design/Liveness%20Probe.md)
  - [Readiness Probe [레디네스 프로브]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Pod-Container%20Design/Readiness%20Probe.md)
  - [Startup Probe [스타트업 프로브]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Pod-Container%20Design/Startup%20Probe.md)

- ### Logging & Monitoring
  - [[사이드카] 사이드카 기본](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/Side-car%20(%EA%B8%B0%EB%B3%B8).md)
  - [[Side-car] EFK 사이드카 설치 및 튜토리얼](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/EFK%20%EC%82%AC%EC%9D%B4%EB%93%9C%EC%B9%B4%20%EC%84%A4%EC%B9%98%20%EB%B0%8F%20%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC.md)
  - [[Monitoring] 모니터링 시스템과 아키텍처](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4%20%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81%20%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B3%BC%20%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.md)
  - [[Monitoring] 쿠버네티스 대시보드](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4%20%EB%8C%80%EC%8B%9C%EB%B3%B4%EB%93%9C.md)

  - [[Monitoring] 프로메테우스[Prometheus]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/Prometheus%20&%20Grafana(%EB%A6%AC%EC%86%8C%EC%8A%A4%20%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81).md)

  - [[Monitoring] istio[서비스 메시 모니터링]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/Istio(%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%20%EB%A9%94%EC%8B%9C%20%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81).md)

  - [[Monitoring] 오토스케일링 HPA 워크스루]](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/%EC%98%A4%ED%86%A0%20%EC%8A%A4%EC%BC%80%EC%9D%BC%EB%A7%81%20HPA%EC%9B%8C%ED%81%AC%EC%8A%A4%EB%A3%A8.md)
https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/%EC%98%A4%ED%86%A0%20%EC%8A%A4%EC%BC%80%EC%9D%BC%EB%A7%81%20HPA%EC%9B%8C%ED%81%AC%EC%8A%A4%EB%A3%A8.md
- ### Security (보안)
  - [Security context](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/Security%20Context.md)
  - [NetworkPolicy](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/NetworkPolicy.md)
  - [쿠버네티스 감사(Audit)](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4%20%EA%B0%90%EC%82%AC(Audit)%20%EA%B8%B0%EB%8A%A5.md)
  - [Trivy(컨테이너 취약점 진단)](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/Trivy%20(%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%20%EC%B7%A8%EC%95%BD%EC%A0%90%20%EC%A7%84%EB%8B%A8).md)
  - [kube-bench 기반 쿠버네티스 보안 점검](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/kube-bench%20%EA%B8%B0%EB%B0%98%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4%20%EB%B3%B4%EC%95%88%20%EC%A0%90%EA%B2%80.md)
  - [Falco 기반 쿠버네티스 컨테이너 보안 모니터링](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/Falco%20%EA%B8%B0%EB%B0%98%20%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4%20%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%20%EB%B3%B4%EC%95%88%20%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81.md)

- ### CI / CD
  - [[CD] GitOps/ArgoCD](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/Gitops-ArgoCD.md)


- ### :books: Certificate
  - [CKA(Certified Kubernetes Administrator)](https://github.com/Virusuki/Kubernetes/blob/main/CKA/CKA.md)
