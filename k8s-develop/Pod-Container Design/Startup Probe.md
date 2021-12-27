# Startup Probe(스타트업 프로브) 개요
- 애플리케이션의 시작 시기를 확인하여 가용성을 높이는 기능
- Liveness와 Readiness의 기능을 비활성화
   - 애플리케이션 최초 실행 시, 애플리케이션이 정상적으로 잘 부팅됐는지 확인(검사)
   - 애플리케이션이 정상적으로 동작 확인이 되었으면, startup probe의 기능은 멈추고 liveness 및 readiness에게 작업을 넘겨줌
