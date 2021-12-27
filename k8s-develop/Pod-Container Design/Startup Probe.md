# Startup Probe(스타트업 프로브) 개요
- 애플리케이션의 시작 시기를 확인하여 가용성을 높이는 기능
- Liveness와 Readiness의 기능을 비활성화
   - 애플리케이션 최초 실행 시, 애플리케이션이 정상적으로 잘 부팅됐는지 확인(검사)
   - 애플리케이션이 정상적으로 동작하는지 확인이 되었으면, startup probe의 기능은 멈추고 
     liveness 및 readiness에게 작업을 넘겨줌    
- Startup probe의 역할은 스타트업할 때까지 충분한 시기를 벌어주는 것
   - 부팅이 오래 걸리는 파드에게 셋팅하는 것이 좋음
   - 점검할 때, 실패해도 넘어감 (실패의 임계치를 정해놓음)



## Startup Probe
- 시작할 때까지 검사를 수행
- http 요청을 통해 검사
- 30번을 검사하며 10초 간격으로 수행
- 300(30*10)초 후에도 파드가 정상 동작하지 않는 경우
   - 300초 동안 파드가 정상 실행되는 시간을 벌어줌

```
ports:
- name: liveness-port
  containerPort: 8080
  hostPort: 8080

livenessProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 1
  periodSeconds: 10

startupProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 30  # 30번 실패해도 괜찮은 임계치, 만약 10(s)*30=300 중간에 부팅이 됐을 때, 중간에 빠져나와서 Liveness / Readiness 프로브를 실행하게 됨
  periodSeconds: 10   # 10초마다 검사
```



Reference:
- https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
