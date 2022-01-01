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

Reference:
- https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/
  
