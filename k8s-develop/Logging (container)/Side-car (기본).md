# 로깅 아키텍처 (Side-Car)
- 사이드카는 오토바이의 사이드 카에서 유래됨
- 이륜차의 보조역할로 기능 확장/향상 (K8S는 파드의 서포트 방식의 기능을 확장/향상)
- 각 사이드카 컨테이너는 공유 볼륨에서 특정 로그파일을 tail한 다음 stdout 스트림으로 리디렉션 하는 방식


<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/img/Sidecar_img.PNG" width="550px" height="400px" title="px(픽셀) 크기 설정" alt="로깅"></img><br/>
<Kubernetes Doc 참조>   



사이드카 컨테이너 생성 실습 - [nginx-sidecar.yaml](https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/nginx-sidecar.yaml)


