# Prometheus(프로메테우스)와 Grafana(그라파나)
- 프로메테우스는 원래 SoundCloud에서 구축된 오픈소스 시스템 모니터링 및 경고 tool kit
- 프로메테우스는 메트릭을 타임시리즈 데이터(예: 메트릭 정보)로 기록
- Lable이라는 선택적 key, value 쌍과 기록된 타임스탬프와 함께 저장

### Prometheus 아키텍처 (그림 참조)
- Prometheus server는 DB라고 볼 수 있음
- Web UI는 시각화하는 도구로 독립적인 오픈소스인 Grafana를 주로 사용함 (Data visualization)
- Prometheus는 내부적으로 alert을 띄울 수 있음
- Jobs/exporters 오브젝트는 노드에 구성되어서, kubelet으로부터 metrics 정보를 Prometheus server(DB)에 저장하고, 
  Grafana UI로 시각화하거나 alertmanager를 통해 이메일, etc 등으로 보냄


<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Promethous_architecture.PNG" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="EFK 웹 사이드카 아키텍처"></img><br/>


## Prometheus(프로메테우스) Install
- 헬름 리파지토리를 통해 프로메테우스 설치
- 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update


Reference:
- https://prometheus.io/docs/introduction/overview/
