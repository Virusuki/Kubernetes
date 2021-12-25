# 쿠버네티스 감사 기능
- 클러스터에서 발생하는 다양한 작업들에 대한 관리자가 모니터링을 하기 위한 audit 기능을 활성화
- 쿠버네티스 audit 기능을 사용해 실시간으로 사용자들이 사용하는 API 정보를 확인이 가능
- 쿠버네티스 audit는 보안 관련 레코드 세트를 시간순으로 저장 가능
- 클러스터 사용자 정보와 kubernetes API를 사용하는 애플리케이션 및 control plane 자체에서 발생하는 활동을 감사

## 감사 기능을 통해 클러스터 관리자가 확인할 수 있는 정보들 리스트
- What happened?                 (무슨 일이 일어났는가?)
- when did it happen??           (언제 일어났는가?)
- who started it?                (누가 시작했습니까?)
- What has happened in the past? (과거에 무슨일이 일이 있었는가?)
- Where was it observed?         (어디서 관찰되었는가?)
- Where did it start?            (어디에서 시작되었는가?)
- Where do the results go?       (결과는 어디로 갔는가?)

## Kube-apiserver를 통해서 실행 및 기록이 시작
- 감사 레코드는 kube-apiserver를 통해 실행
- 실행의 각 단계에 대한 각 요청은 감사 이벤트를 생성
- 특정 정책에 따라 사전 처리되고 백엔드에 기록
- 각 요청은 연결된 단계에 따라 기록하는 정보가 다름
  - 요청에 따른 기록 정보
    - RequestReceived : 감사 핸들러가 요청을 받는 즉시 그리고 핸들러 체인 아래로 위임되기 전에 생성된 이벤트에 대한 단계
    - ResponseStarted : 응답 헤더 전송되었으나 응답 본문이 전송되기 전 상태다. 이 단계는 장기 실행 요청(예: watch)에 대해 생성
    - ResponseComplete : 응답 본문이 완료되었으며 더이상 바이트가 전송되지 않는 단계
    - Panic : 패닉이 발생 시, 생성되는 이벤트
