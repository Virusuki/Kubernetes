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

## Helm(헬름) 설치 (프로메테우스 설치하기 앞서)
- 헬름 설치 command
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm -h
```


- 헬름의 기본 스토리지 클래스로 rook-ceph-block을 사용하도록 설정
- 프로메테우스는 헬름차트로 인해 구성되므로, 내부적으로 스토리지 클래스를 활용함 그래서, rook-ceph는 설치되어 있어야 함.(포인트!!)
```
kubectl patch storageclass rook-ceph-block -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```


## Prometheus(프로메테우스) Install
- 헬름 리파지토리를 통해 프로메테우스 설치
- Prometheus(프로메테우스)와 Grafana(그라파나)에 대한 헬름 저장소 추가
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

- 헬름 배포를 위한 프로메테우스 및 그라파나 디렉토리 구성
```
mkdir grafana_prometheus
cd grafana_prometheus
```

- values-prometheus.yaml 파일 생성
```values-prometheus.yaml 
cat <<EOF > values-prometheus.yaml # 프로메테우스를 사용하는데 있어서, 변수들을 value파일에 구성함
server:
  enabled: true    # 서버를 활성화

  persistentVolume:
    enabled: true   # PVC 활용 체크
    accessModes:
      - ReadWriteOnce
    mountPath: /data # 프로메테우스가 데이터를 저장하는 공간
    size: 10Gi
  replicaCount: 1

  ## Prometheus data retention period (default if not specified is 15 days)
  ##
  retention: "15d"  # 15일 기간 정도 저장
EOF
```

- values-grafana.yaml 파일을 생성 및 pvc 구성하여 설정정보를 유지
```
cat << EOF > values-grafana.yaml
replicas: 1

service:
  type: NodePort

persistence:
  type: pvc
  enabled: true
  # storageClassName: default
  accessModes:
    - ReadWriteOnce
  size: 10Gi
  # annotations: {}
  finalizers:
    - kubernetes.io/pvc-protection

# Administrator credentials when not using an existing secret (see below)
adminUser: admin
adminPassword: test1234!234
EOF
```


- 프로메테우스를 위한 Namespace를 구성하고 위 설정대로 yaml 파일을 배포
```
kubectl create ns prometheus
helm install prometheus prometheus-community/prometheus -f values-prometheus.yaml -n prometheus
helm install grafana grafana/grafana -f values-grafana.yaml -n prometheus
```

- Grafana login 화면   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/prometheus_login.PNG" width="500px" height="350px" title="px(픽셀) 크기 설정" alt="Grafana login"></img><br/>


- grafana 대시보드에서 프로메테우스 연결 과정   
- Configuration -> Add data source 클릭

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/prometh_grafana_connection.PNG" width="500px" height="300px" title="px(픽셀) 크기 설정" alt="connection"></img><br/>


- Prometheus를 선택

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Prometheus_select.PNG" width="500px" height="300px" title="px(픽셀) 크기 설정" alt="Prometheus select"></img><br/>


- Prometheus URL 도메인 정보 입력 (prometheus-server) 및 save & test 클릭
root@namuk-01:~# kubectl get svc -n prometheus

```
NAME                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
grafana                         NodePort    10.102.253.89    <none>        80:30845/TCP   8h
prometheus-alertmanager         ClusterIP   10.111.117.233   <none>        80/TCP         8h
prometheus-kube-state-metrics   ClusterIP   10.108.200.135   <none>        8080/TCP       8h
prometheus-node-exporter        ClusterIP   None             <none>        9100/TCP       8h
prometheus-pushgateway          ClusterIP   10.106.116.188   <none>        9091/TCP       8h
prometheus-server               ClusterIP   10.107.17.248    <none>        80/TCP         8h
```


<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/prometheus_url_save.PNG" width="850px" height="900px" title="px(픽셀) 크기 설정" alt="Prometheus URL"></img><br/>




- grafana 공식사이트의 대시보드에 접속 (대시보드 다운받기를 위해 진행)
- 쿠버네티스를 모니터링 하고 싶으면 "kubernetes" 검색 및 카테고리를 통해 원하는 대시보드를 다운로드
<img src="https://grafana.com/grafana/dashboards/?search=kubernetes" width="850px" height="900px" title="px(픽셀) 크기 설정" alt="Prometheus URL"></img><br/>   


- Grafana 대시보드 검색된 번호
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/grafana_dashboard_search_number.PNG" width="500px" height="500px" title="px(픽셀) 크기 설정" alt="Prometheus URL"></img><br/>

   

- grafana의 Import dashboard from file or Grafana.com
- import via grafana.com의 찾은 대시보드 번호 import
- Grafana의 대시보드 import UI 이동 [+ -> import] 및 검색된 번호 입력
   
   
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/315number.PNG" width="500px" height="350px" title="px(픽셀) 크기 설정" alt="Prometheus URL"></img><br/>



Reference:
- https://prometheus.io/docs/introduction/overview/
