# Flask?
- 파이썬의 대표적인 웹 개발 프레임워크
- 마이크로 프레임워크, 가볍고 간단
- 지정한 라이브러리와 패키지만 설치됨 -> 효율성, 자유도가 높음

## Flask 앱 구조
- 플라스크 앱은 다음 과정을 통해 호출
   - (1) 특정 URL 호출 --> (2) URL 지정 뷰 함수 호출 --> (3) 논리 실행 --> 
     (4) 논리 결과 응답 전송 --> (5) 응답값 HTML 표현 --> (6) 클라이언 전달 


## Flask 실습 환경 구성
- Rest api 라이브러리 설치

```
pip install flask_restx
```




### Host -> docker로 파일 전송 방법
전송할 파일  컨테이너명:저장할 파일 경로
docker cp alldb.dmp ContainerID:/alldb.dmp


Reference:
- https://restfulapi.net/http-methods/  (REST API 참조)
