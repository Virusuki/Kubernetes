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
(1). 앱 배포 (flask로 배포)
- Jupyterlab 배포

jupyterlab-app.yaml

```   
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jupyter-ceph-user6
  labels:
    app: jupyter-ceph-user6
spec:
  selector:
    matchLabels:
      app: jupyter-ceph-user6
  replicas: 2
  template:
    metadata:
      labels:
        app: jupyter-ceph-user6
    spec:
      containers:
      - name: jupyter-ceph-user6
        image: namuk2004/kbri-jupyterlab
        #imagePullPolicy: Never
        ports:
        - containerPort: 9000

---
apiVersion: v1
kind: Service
metadata:
  name: service-jupyter-ceph-user6
spec:
  sessionAffinity: ClientIP
  selector:
    app: jupyter-ceph-user6
  ports:
  - protocol: "TCP"
    port: 9000
    targetPort: 9000
  type: NodePort
```

2개의 replica를 가진 deployment와 Nodeport로 구성된 Service를 배포했으며, 9000 포트를 통해 접근 가능하다. 위의 예제소스 yaml을 실행합니다.
다음은 Jupyterlab을 deploy하는 command 입니다.



## EFK(Elasticsearch + Fluentd + Kibana) 배포 과정

EFK는 elastic-project namespace에 배포하였으며, 각 yaml 파일을 통해 배포가 가능합니다.


Elasticsearch.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: elasticsearch
  namespace: elastic-project
  labels:
    app: elasticsearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: elastic/elasticsearch:7.14.1
        env:
        - name: discovery.type
          value: single-node
        ports:
        - containerPort: 9200
        - containerPort: 9300
        
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: elasticsearch
  name: elasticsearch-svc
  namespace: elastic-project
spec:
  ports:
  - name: elasticsearch-rest
    nodePort: 30920
    port: 9200
    protocol: TCP
    targetPort: 9200
  - name: elasticsearch-nodecom
    nodePort: 30930
    port: 9300
    protocol: TCP
    targetPort: 9300
  selector:
    app: elasticsearch
  type: NodePort
```

```
$ kubectl apply -f Elasticsearch.yaml
$ kubectl port-forward svc/elasticsearch-svc -n elastic 9200:9200 --address=0.0.0.0 &
```



Kibana.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  namespace: elastic-project
  labels:
    app: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: elastic/kibana:7.14.1
        env:
        - name: SERVER_NAME
          value: kibana.kubenetes.example.com
        - name: ELASTICSEARCH_URL
          value: http://elasticsearch-svc:9200
        ports:
        - containerPort: 5601
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: kibana
  name: kibana-svc
  namespace: elastic-project
spec:
  ports:
  - nodePort: 30561
    port: 5601
    protocol: TCP
    targetPort: 5601
  selector:
    app: kibana
  type: NodePort

```

```
$ kubectl apply -f Elasticsearch.yaml
$ kubectl port-forward svc/elasticsearch-svc -n elastic 9200:9200 --address=0.0.0.0 &
```


또는, Docker container를 사용한 Kibana run도 가능합니다.

```
$ docker run -d --name kib01-test --net elastic -p 5601:5601 -e "ELASTICSEARCH_HOSTS=http://es01-test:9200" kibana:7.14.1

*단, Kibana 애플리케이션을 배포할때는 Elasticsearch의 버전과 동일해야 합니다.!!
```

Fluentd.yaml

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: elastic-project

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: fluentd
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
  namespace: elastic-project

---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: elastic-project
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
            value: "elasticsearch-svc"
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: "9200"
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: "http"
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
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

