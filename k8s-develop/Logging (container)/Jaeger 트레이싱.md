# Jaeger 트레이싱 시스템
- 마이크로 서비스를 위한 오픈 소스 추적 시스템이며, OpenTracing 표준을 지원
- Jaeger(예거)는 추적, 근본 원인 분석, 서비스 종속성 분석 등 배포
- Go, Java, Javascript, nodejs, python, C++를 위한 라이브러리 포함
- 헬름차트나 istio 등을 활용해 편리하게 설치 가능 

- 예러 트레이싱 용어집
   - 에이전트 - 사용자 데이터그램 프로토콜을 통해 전송된 범위에 대해 듣는 네트워크 데몬
   - 클라이언트 - 분산 추적을 위한 OpenTracing API를 구현하는 구성 요소
   - 컬렉터 - 범위를 수신하고 처리할 큐에 추가하는 구성 요소
   - 콘솔 - 사용자가 분산 추적 데이터를 시각화할 수 있는 UI
   - 쿼리 - 저장소에서 추적을 가져오는 서비스
   - 범위 - 이름, 시작시간 및 작업의 기간을 포함하는 예거에서 작업의 논리적 단위
   - 추적 - 예거가 실행 요청을 제시하는 방식, 추적은 하나 이상의 범위로 구성

[예거 시스템 그림]

## Jaeger(예거) Install
- 추적 정보를 수집, 저장 및 표시하기 위해 분산 구성 요소 집합
- 전체 예거 시스템을 실행하는 '올인원' 이미지로 설치
- 만약 쿠버네티스로 배포하고 싶은 경우, 헬름차트 or istios 등 활용해 설치 가능
```
docker run -d --name jaeger -p 16686:16686 \
           -p 6831:6831/udp \
           jaegertracing/all-in-one:1.22
```
- 예거 클라이언트 설치 과정 
- jaeger-client 및 requests는 lib
```
apt install python3-pip -y
pip3 install jaeger-client
pip3 install requests
```
