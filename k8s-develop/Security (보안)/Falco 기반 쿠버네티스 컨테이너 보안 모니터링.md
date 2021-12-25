# flaco 개요
- 클라우드 네이티브 런타임 보안 프로젝트인 falco는 쿠버네티스 위협 탐지 엔진
- CNCF에 합류한 최초의 런타임 보안 프로젝트
- 예기치 않은 애플리케이션 동작을 감지하고 런타임 시 위협에 대해 경고

## 호스트에 falco 설정 정보 확인
- log_syslog: true 옵션은 /var/log/syslog에 falco 관련 로그를 함께 로깅한다는 의미
- rules_file 섹션에는 실행할 때 참조하는 룰의 위치가 명시됨 


## flaco 설치
