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

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Jaeger_images/Jaeger_system.PNG" width="600px" height="450px" title="px(픽셀) 크기 설정" alt="Jaeger Architecture"></img><br/>

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

- 파이썬 프로그램에 트레이싱 예제
- init_tracer 함수는 db에 등록할 정보와 로깅 레벨 등을 설정한 추적 객체를 구성해 반환
   - 예거 클라이언트를 구성하고, 함수의 service인자를 config 설정 관련된 셋팅에서 service_name=service 인자값 받고
     return config.initialize_tracer() 반환하는데 config.initialize_tracer() 객체를 통해 로깅정보를 남긴다.

- first-service 트레이서를 구성
   - first-service는 start_span('get-ip-api-jos')이름으로 로깅하는데 
   - 트레이서를 활용해 get-ip-api-jobs 라는 span을 구성
   - 웹 요청 결과를 span에 데이터 추가
   - python-jaeger.example.py
```
import logging
from jaeger_client import Config
import requests

def init_tracer(service):
    logging.getLogger('').handlers = []
    logging.basicConfig(format='%(message)s', level=logging.DEBUG)

    config = Config(
        config={
            'sampler': {
                'type': 'const',
                'param': 1,
            },
            'logging': True,
        },
        service_name=service,
    )

    # this call also sets opentracing.tracer
    return config.initialize_tracer()

tracer = init_tracer('first-service')

with tracer.start_span('get-ip-api-jobs') as span:
    try:
        res = requests.get('http://ip-api.com/json/naver.com')  # 해당 사이트에 접근 (도메인 -> 지도 정보를 바꿔주는 url)
        result = res.json()                           # json 형태로 값 넘김
        print('Getting status %s' % result['status'])
        span.set_tag('jobs-count', len(res.json()))
        for k in result.keys():                     # key를 파싱해서 정보를 받아옴
            span.set_tag(k, result[k])    # span.set_tag 정보

    except:
        print('Unable to get site for')

input('')
input('') ## 해당 라인은 너무 빨리 끝나서 예거 UI에 기록 유지를 위해 추가 (옵션)
```
- 위의 파이썬 코드
   - first-service => (로그) get-ip-api-jobs => status":"success","country":"South Korea","countryCode":"KR", 등등 key-value를 묶어줌
- MSA 아키텍처에서 각각의 애플리케이션 마다 로그 기능을 만들어주면 현재 어떤 작업들이 내부적으로 진행되는지 남길 수 있도록 만들 수 있음


- python3 python-jaeger.example.py 파일 실행 
- 로깅을 시작하고, first-service 인 곳에 get-ip-api-jobs 부분에다가 리포팅을 했는 것을 나타내고 있음
```
root@namuk-01:/home/ubuntu/Jeager# python3 python-jaeger.example.py
Initializing Jaeger Tracer with UDP reporter
Using selector: EpollSelector
Using sampler ConstSampler(True)
opentracing.tracer initialized to <jaeger_client.tracer.Tracer object at 0x7fce8ab60198>[app_name=first-service]
Starting new HTTP connection (1): ip-api.com
http://ip-api.com:80 "GET /json/naver.com HTTP/1.1" 200 269
Getting status success
Reporting span a325e04a249bbb5:bb50604f3468ea3e:0:1 first-service.get-ip-api-jobs
```

- Jaeger 메인 UI   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Jaeger_images/Jaeger_UI.PNG" width="700px" height="580px" title="px(픽셀) 크기 설정" alt="Jaeger main UI"></img><br/>


- Jaeger trace UI   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Jaeger_images/Jaeger_trace.PNG" width="850px" height="700px" title="px(픽셀) 크기 설정" alt="Jaeger trace UI"></img><br/>


- Jaeger trace first-service 추적 UI   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Jaeger_images/Jaeger_trace_first_service.PNG" width="850px" height="700px" title="px(픽셀) 크기 설정" alt="Jaeger first-ervice"></img><br/>   


