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
