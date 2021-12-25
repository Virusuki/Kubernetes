# Trivy
 - Aquasecurity(해외)에서 배포한 오픈소스 도구이다.
 - 취약점을 간단히 스캔할 수 있는 도구
   - 컨테이너 이미지, 파일시스템 및 Git 리포지토리의 취약점 등 다양한 진단
   - OS 패캐지 / 언어별(composer, npm, yarn 등) 패키지의 취약점 진단
   - Dockerfile 및 Kubernetes와 같은 lac(코드) 파일로 인프라를 스캔하여 배포를 공격 위험에 노출시키는 잠재적 구성 문제를 검색하는 기능

## 도커 컨테이너를 활용한 Trivy 설치
  - 컨테이너를 사용해서 설치하면 호환성 문제 없이 빠른 시간에 Trivy를 구성할 수 있다.
  - -v 옵션을 사용하여 캐시 디렉토리를 마운트 
  - 소켓을 공유하여 현재 호스트에 구성된 도커 소켓을 통해 이미지를 제어할 수 있다.
    - 도커에서 배포된(실행된) 컨테이너로 다시 도커를 제어할 수 있는 기능을 제공해주기 위해서는 소켓을 공유한다.
  
```
docker run --rm -v trivy-cache:/root/.cache/ -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest
```

## Trivy를 활용한 도커 이미지 취약점 진단
  - Trivy에 image 명려을 사용하면 도커 이미지의 취약점을 진단
  - 현재 구성되어 있는 다양한 모듈들의 취약점을 도출

```
docker run --rm -v trivy-cache:/root/.cache/ -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image namuk2004/kbri-jupyterlab
```

### 실행 결과
- 실행결과에서 namuk2004/kbri-jupyterlab (ubuntu 18.04)
  - Total: 252 (UNKNOWN:0, LOW:160, MEDIUM:92, HIGH:0, CRITICAL:0) <-- UNKNOWN(알려지지않은),LOW/MEDIUM(공격이 어려운 쪽 레벨 분류, HIGH/CRITICAL(접근성이 편한 경우,취약점이 높다)

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/Security%20(%EB%B3%B4%EC%95%88)/img/Trivy%EC%8B%A4%ED%96%89%EA%B2%B0%EA%B3%BC.PNG" width="750px" height="850px" title="px(픽셀) 크기 설정" alt="Trivy 실행결과"></img><br/>
