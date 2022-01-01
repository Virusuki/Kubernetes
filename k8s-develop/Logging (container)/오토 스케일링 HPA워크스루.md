# 오토스케일링 HAP 워크스루란
- 파드 스케일링의 두 가지 방법
   - HPA: 파드 자체를 복제하여 처리할 수 있는 파드의 (Horizontal Pod Autoscaler) <-- 포인트!
   - VPA: 리소스를 증가시켜 파드의 사용 가능한 리소스를 늘리는 방법 (Vertical Scaling)
   - CA: 번외로 클러스터 자체를 늘리는 방법 (노드 추가)

- HPA(Horizontal Pod Autoscaler)
   - 쿠버네티스에는 기본 오토스케일링 기능 내장
   - CPU 사용률을 모니터링하여 실행된 파드의 개수를 늘리거나 줄임 


## HPA(Horizontal Pod Autoscaler) 설정 방법
- 명령어를 사용하여 오토스케일 저장
- --max 최대개수를 몇개로 할지(그 이상으로 올라가지 않음), --min 최소개수는 몇개를 유지할지 지정(그 이하로 떨어지지 않음)

```
# kubectl autoscale deployment my-app --max 6 --min 4 --cpu-percent 50
```

- HPA yaml을 작성하여 타겟 파드 지정
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: myapp
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 30
```
  
- HPA 테스트
   - php-apache 서버 구동 및 노출

- application/php-apache.yaml 테스트
- 중요한 점은 HPA를 할려면 Deployment의 resources에서 limits과 requests를 정해야 함
- 자원 사용량 100%의 기준은 resources의 requests(최소사양)로 HPA에서는 정해짐.
- HPA는 사용자가 지정해준 %를 초과하면 그때 복제하게 되어 있음.
- 따라서, 100% 기준은 requests에서 가져옴 (CPU200m가 사용되었을 때 -> 100%가 됨),
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  replicas: 1
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: k8s.gcr.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m

---

apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache
```

- HPA는 pod의 정보를 계속 모니터링하면서 pod가 너무 많은 자원을 사용하고 있으면 Deployment에 HPA가 제어 및 수행 
- (그림 참조)

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/HPA%20process.PNG" width="550px" height="400px" title="px(픽셀) 크기 설정" alt="kiari conne"></img><br/>

### HPA 테스트
- !! 테스트에 앞서, metrics 서버가 설치되어 있어야 함 (참조)
```
https://github.com/kubernetes-sigs/metrics-server
```
- php-apache 테스트 생성 
```
kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
```

- HPA 생성
- CPU가 50% 넘어가면 최소1개, 최대 10개를 유지하도록 구성
```
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

- 부하 증가
- 부하증가함에 따라 오토스케일러가 어떻게 반응하는지 체킹
```
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```

- kubectl get hpa -w 를 통해 부하증가 상태 확인

```
root@namuk-01:~# kubectl get hpa -w
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         10        1          7m41s
php-apache   Deployment/php-apache   71%/50%   1         10        1          8m16s
php-apache   Deployment/php-apache   258%/50%   1         10        2          8m31s
php-apache   Deployment/php-apache   246%/50%   1         10        4          8m46s
php-apache   Deployment/php-apache   161%/50%   1         10        6          9m1s
php-apache   Deployment/php-apache   62%/50%    1         10        6          9m16s
php-apache   Deployment/php-apache   48%/50%    1         10        6          9m31s
php-apache   Deployment/php-apache   41%/50%    1         10        6          9m46s
php-apache   Deployment/php-apache   50%/50%    1         10        6          10m
php-apache   Deployment/php-apache   39%/50%    1         10        6          10m
php-apache   Deployment/php-apache   4%/50%     1         10        6          10m
php-apache   Deployment/php-apache   0%/50%     1         10        6          10m
```






Reference:
- https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/
  
