# Readiness Probe(레디네스 프로브) 개요
- 파드가 준비된 상태에 있는지 확인하고 정상 서비스를 시작하는 기능
- 파드가 적절하게 준비되지 않은 경우 로듭밸런싱을 하지 않음


## Readiness Probe도 Liveness Probe와 동일하게 점검하는 3가지 방식은 같다.
1. CommandLine
   - 0이면 정상
   - 0 외에 비정상
2. TCP (3 way handshake) : 3-way handshake가 잘 맺어진다고 하면 통신이 원활하다라 판단하고 파드가 살아있다고 판별
   - #1. SYN
   - #2. SYN + ACK
   - #3. ACK
3. http : get요청을 했을 때, 응답이 온다면 정상으로 판단
   - 200, 300이 오면 정상 (200~400미만은 정상)
   - 400, 500은 오류 (실패로 간주)

- 단, Readiness Probe는 Liveness Probe와 다른 점은 아래의 그림과 같이, 외부에서 서비스로 들어오는 사용자 request를 pod에 적절하게 분배하는 것이 서비스의 역할이다.
- 그런데, 파드가 적절하게 동작하지 않는다면(파드가 응답없을 때) 그 파드에는 서비스를 할 수 없도록 막아야한다. 나머지 2개의 파드가 있으므로, 미응답 파드의 서비스는 연결은 끊는다.
- 이런 기능을 위해서 Readiness Probe가 있으며 즉, 로드밸런싱 하지않도록 구성해주는게 Readiness probe의 역할이다.

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Pod-Container%20Design/files/img/Pod_service_action.PNG" width="550px" height="300px" title="px(픽셀) 크기 설정" alt="Pod service action"></img><br/>

### Readiness와 Liveness 예제
#### Readiness TCP 설정
- 준비 프로브는 8080포트를 검사
- 5초 후부터 검사 시작
- 검사주기는 10초
   - 서비스를 시작해도 된다.

#### Liveness TCP 설정
- 활성화 프로브는 8080포트를 검사
- 15초 후부터 검사시작
- 검사 주기는 20초
  - 컨테이너를 재시작하지 않아도 된다.

```
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
  - name: goproxy
    image: k8s.gcr.io/goproxy:0.1
    ports:
    - containerPort: 8080
    readinessProbe:  # 레디네스 프로브가 실패하면 서비스와 연결이 끊기는 결과가 나옴
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:   # 라이브네스 프로브가 실패하면 재시작함 (handshake 맺어지면 정상적으로 동작)
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
```


Reference:
- https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
