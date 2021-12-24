# EFK 기반 웹 로그 수집을 위한 사이드 컨테이너 및 파이프 라인 구축 및 튜토리얼

## EFK 란?   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Logging%20(container)/files/EFK%20%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.PNG" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="EFK 웹 사이드카 아키텍처"></img><br/>


- EFK stack은 Elasticsearch + Fluentd + Kibana 3개의 플랫폼 조합을 의미하며, 쿠버네티스 클러스터 환경에서 로그를 수집 및 검색과 시각화할 수있는구조이다. 본 튜토리얼 문서에서는 EFK 아키텍처의 각 플랫폼에 대해 자세히 다루지는 않고, 간단한 튜토리얼을 통해 설치 과정을 담았습니다.쿠버네티스 클러스터 각 노드에 fluentd가 daemonset으로 배치하여 log를 수집합니다. 이를 Elasticsearch에 전송하고, elasticsearch는 수집한 로그를저장하고, 요청에 따른 검색 한다. elasticsearch가 감지한 로그는 kibana에서 감지된 로그정보를 시각화합니다.
   
```   
10. 컨테이너 이름 변경 - rename    
sudo docker rename apache apache_server   
```
```   
11. 컨테이너 내부 파일 복사 - cp   
sudo docker container cp apache:/usr/local/apache2/htdocs/index.html /tmp/index.html
```
```   
12. 컨테이너 변경 사항 확인 - diff   
sudo docker diff apache
```


[Elasticsearch] https://www.elastic.co/kr/what-is/elasticsearch   
[Fluentd] https://www.fluentd.org/   
[Kibana] https://www.elastic.co/kr/what-is/kibana   

- 
