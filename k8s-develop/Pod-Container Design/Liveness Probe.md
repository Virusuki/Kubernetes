# Liveness Probe(라이브니스 프로브) 개요
- 컨테이너가 살아있는지 판단하고 다시 시작하는 기능
  - 애플리케이션 생성 시, 예기치 못한 상황 발생 대비(예:병목현상, 메모리 과부하로 인한 할당 실패로 동작 실패) 
- 컨테이너의 상태를 스스로 판단해 교착 상태에 빠진 컨테이너를 재시작
- 버그가 생겨도 높은 가용성을 보임

## Liveness Probe를 통해서 컨테이너가 살았는지 판단하는 방법 3가지
1. CommandLine
   - 0이면 정상
   - 0 외에 비정상
2. TCP (3 way handshake) : 3-way handshake가 잘 맺어진다고 하면 통신이 원활하다라 판단하고 파드가 살아있다고 판별함
   - #1. SYN
   - #2. SYN + ACK
   - #3. ACK
3. http : get요청을 했을 때, 응답이 온다면 정상으로 판단함
   - 200, 300이 오면 정상 (200~400미만은 정상)
   - 400, 500은 오류 (실패로 간주)


- Liveness 커맨드 설정 - 파일 존재 여부 확인
   - 리눅스 환경 command 실행 성공 시 0 (컨테이너 유지)
   - 실패하면 그 외 값 출력 (컨테이너 재시작)

- Liveness Probe(라이브네스 프로브) 예제
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:           # Liveness는 검사를 하고자하는 컨테이너에 설정
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600  
    # touch /tmp/healthy; 정상동작 중,,, sleep 30초간 쉬었다가 바로 healthy파일 삭제되면서 파일이 없기 떄문에, 이후 라이브네스프로브가 실패하게 되고, 컨테이너가 재시작됨
   
    livenessProbe:     # 라이브네스가 실행되면서 컨테이너가 정상적으로 동작하는지 체크
      exec:
        command:
        - cat
        - /tmp/healthy  
      initialDelaySeconds: 5  # 처음에 5초를 딜레이 했다가 periodSeconds를 통해 5초 주기로 cat /tmp/healthy를 실행함
      periodSeconds: 5
```


- Liveness 웹 설정 - http 요청 확인
   - 서버 응답 코드가 200이상 400미만 (컨테이너 유지)
   - 서버 응답 코드가 그 외일 경우 (컨테이너 재시작)

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:  
# Liveness에서 3초기다렸다가 3초 주기로 검사, httpGet 포트8080에 요청하는게 프로세스의 역할. 3초마다 /server에 질의 요청하면서 만약 400-500이 발생하면 Liveness Probe에서 재시작함
  (go 언어로 구성되어 있으며, 처음 10초간 200을 반환하는데, 이후 500을 반환하기 시작하면서 Liveness Probe가 실패하는것을 간주하면서 재시작하게끔 구성되어 있음)
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```


Reference:
- https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
