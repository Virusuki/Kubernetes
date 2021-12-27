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
  
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

- Reference:
