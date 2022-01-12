# MSA를 위한 REST API 개념
- Representational State Transper의 약자로 자원을 이름으로 구분하여 주고 받는 모든 것을 의미
- HTTP URL로 자원을 명시하고 HTTP Method로 자원의 CRUD 오퍼레이션을 적용

### Rest API 장점
- HTTP 표준 프로토콜에 따르는 모든 프로토콜에서 사용 가능하며 디자인 문제 최소화
- 서버와 클라이언트 역할 분리 등이 있음

### REST API 단점
- 구형 브라우저가 모든 메서드를 지원하지는 않아 사용할 수 있는 메서드가 제한적임


### REST API 특징
- 서버와 클라이언트 구조
- 무상태, 클라이언트 context를 서버에 저장하지 않아 구현이 단순하며, 클라이언트 요청만 단순 처리
- 캐시 처리가 가능
- 계층화, 클라이언트는 REST API 서버만 호출하며 다중 계층으로 구성

### REST API 설계 기본 규칙
- REST API는 도큐먼트, 컬렉션, 스토어로 구성
   - 도큐먼트: 데이터베이스 레코드
   - 컬렉션: 서버에서 관리하는 리소스, 디렉터리
   - 스토어: 클라이언트에서 관리하는 리소스 저장소

- REST API 설계 규칙
   - 슬래시 구분자(/)는 계층 관계를 나타내는데 사용 (예: IP주소/User/OOO/등)
   - URI 마지막 문자로 슬래시(/)를 포함하지 않으며 URI 경로의 마지막에는 슬래시(/)를 사용하지 않는다
   - 하이픈(-)은 URI 가독성을 높이는데 사용
   - 언더바(_)는 URI 사용하지 않음
   - URI 경로에는 소문자가 적합
   - 파일확장자는 URI에 포함하지 않음
   - 리소스 간에는 연관 관계가 있는 경우 /리소스명/리소스 ID/관계가 있는 다른 리소스명으로 표기

- REST API 잘 설계하기
   - 팀 멤버와 유저들도 제대로 활용할 수 있음
   - API는 기본적인 CRUD를 활용
   - 명확한 패턴이 필요
   - URL에서 동사를 사용하지 않아야 함 (대신 HTTP request mothod를 사용)
   - 자동차 판매점 API 예제
       - 자동차를 추가
       - 자동차를 검색
       - 자동차 엔진이나 옵션, 소개글 등 조회
       
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/MSA(%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98)/img/MSA%EA%B8%B0%EB%B3%B8%EC%84%A4%EA%B3%84.PNG" width="600px" height="190px" title="px(픽셀) 크기 설정" alt="MSA 설계 예"></img><br/>


  










