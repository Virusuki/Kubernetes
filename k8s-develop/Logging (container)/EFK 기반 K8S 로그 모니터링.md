# EFK 기반 웹 로그 수집을 위한 사이드 컨테이너 및 파이프 라인 구축 및 튜토리얼

## EFK(Elasticsearch, Fluentd, Kibana) 란?   
- 쿠버네티스에서 EFK를 사용하면 도커의 각 컨테이너 로그를 수집, 시각화, 분석 가능
- 해당 환경은 메모리 8GB 이상 필요
- 엘라스틱스택은 주로 비츠나 로그스태시를 사용하는 것이 일반적
- 또는, 쿠버네티스에서는 fluentd를 사용해서 수집하는 것이 트렌드


<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/EFK%20Architecture_img.PNG" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="EFK 웹 사이드카 아키텍처"></img><br/>



- EFK stack은 Elasticsearch + Fluentd + Kibana 3개의 플랫폼 조합을 의미하며, 쿠버네티스 클러스터 환경에서 로그를 수집 및 검색과 시각화할 수있는구조이다. 본 튜토리얼 문서에서는 EFK 아키텍처의 각 플랫폼에 대해 자세히 다루지는 않고, 간단한 튜토리얼을 통해 설치 과정을 담았습니다.
- 쿠버네티스 클러스터 각 노드에 fluentd가 daemonset으로 배치하여 log를 수집합니다. 이를 Elasticsearch에 전송하고, elasticsearch는 수집한 로그를저장하고, 요청에 따른 검색 한다. elasticsearch가 감지한 로그는 kibana에서 감지된 로그정보를 시각화합니다.


[Elasticsearch] https://www.elastic.co/kr/what-is/elasticsearch   
[Fluentd] https://www.fluentd.org/   
[Kibana] https://www.elastic.co/kr/what-is/kibana   
   
  
[구축 과정]
(1). EFK 다운로드
- https://github.com/Virusuki/Kubernetes/tree/main/k8s-develop/Logging%20(container)/files/EFK%20example_stable
- yaml 복사 

```
kubectl -f 
elasticsearch.yaml
fluentd.yaml
kibana.yaml
ns.yaml
```


- fluentd.yaml 핵심 포인트
- fluentd에 권한을 줌
```
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: fluentd
  namespace: kube-system
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: kube-system
---
apiVersion: apps/v1
kind: DaemonSet   # 로그를 수집하는 역할
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
    version: v1
spec:
  selector:
    matchLabels:
      k8s-app: fluentd-logging
      version: v1
  template:
    metadata:
      labels:
        k8s-app: fluentd-logging
        version: v1
    spec:
      serviceAccount: fluentd
      serviceAccountName: fluentd
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1-debian-elasticsearch
        env:
        - name:  FLUENT_ELASTICSEARCH_HOST
          value: elasticsearch-svc.elastic
        - name:  FLUENT_ELASTICSEARCH_PORT
          value: "9200"
        - name: FLUENT_ELASTICSEARCH_SCHEME
          value: http
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog   
        hostPath:
          path: /var/log   ## 노드에서 로그를 저장하는 기본적인 위치 
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers   ## 도커 컨테이너의 로그 저장 
```


- 4개 yaml 파일 실행 후, kibana svc를 통해 NodePort 확인하고 접속한다.

```
kubectl get svc -n elastic
```

```
root@namuk-01:/home/ubuntu/EFK# kubectl get svc -n elastic
NAME                TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                         A                                                                                                                       GE
elasticsearch-svc   NodePort   10.96.189.208   <none>        9200:30920/TCP,9300:30930/TCP   1                                                                                                                       7m
kibana-svc          NodePort   10.108.21.97    <none>        5601:30561/TCP                  1 
```

- kibana main 화면   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/EFK_images/main_display.PNG" width="600px" height="500px" title="px(픽셀) 크기 설정" alt="kibana main"></img><br/>



- index pattern: logstash   
  
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/EFK_images/logstash_indexpattern.PNG" width="600px" height="500px" title="px(픽셀) 크기 설정" alt="index pattern"></img><br/>

- discover   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/EFK_images/Discover.PNG" width="600px" height="500px" title="px(픽셀) 크기 설정" alt="discover"></img><br/>

