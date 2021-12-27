# Readiness Probe(레디네스 프로브) 개요
- 파드가 준비된 상태에 있는지 확인하고 정상 서비스를 시작하는 기능
- 파드가 적절하게 준비되지 않은 경우 로듭밸런싱을 하지 않음


## Readiness Probe도 Liveness Probe와 동일하게 점검하는 3가지 방식이 같음
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
