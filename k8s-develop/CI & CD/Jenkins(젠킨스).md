# 젠킨스란?
- 어떤 규모로든 훌륭한 시스템을 구축하기 위한 도구
- 선도적인 오픈소스 자동화 서버
- 모든 프로젝트를 빌드, 배포 및 자동화하는데 지원 
- 수백 개의 플러그인 제공

## Jenkins(젠킨스) Install
- 도커 이미지를 사용해 jenkins 설치 및 배포
- Jenkins를 배포할때, 일부 디렉토리를 공유하도록 설정했으며, 도커 소켓 또한 공유하도록 구성
- 이 소켓을 사용해 jenkins는 호스트에 설치된 도커 기능을 사용할 수 있음

```
docker run -d -p 8080:8080 --name jenkins -v /home/jenkins:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -u root jenkins/jenkins:lts

#  -v /var/run/docker.sock:/var/run/docker.sock 도켓 소켓을 공유하는 이유는 본 호스트(우분투)에 구성해놓은 도커 기반에 젠킨스를 올리고,
   젠킨스에다가 도커를 컨트롤할 수 있는 소켓을 준다. 젠킨스에서 소켓을 컨트롤해서 도커를 다시 한번 컨트롤할 수 있도록 만들어주는 시스템.
   이런 방식을 DooD(Docker Out of Docker)라 부름. 도커->젠킨스 올린다음 다시 ->도커는 젠킨스에 컨트롤 받는 방식
   반면, DinD는 도커를 통해서 컨테이너가 올라가면 컨테이너 안에 도커가 있으며, 도커2개가 돌아가는 방식
   
   -u root jenkins/jenkins:lts 에서 root권한을 줬는 이유는 도커에 접근을 하기 위해,,,
```

- Jenkins 컨테이너에도 docker 클라이언트가 필요하므로 도커를 설치
```
# jenkins에 docker client 설치
docker exec jenkins apt update
docker exec jenkins apt install -y docker.io
```

- Jenkins의 초기화에 사용할 패스워드를 조회
```
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

- Jenkins가 8080포트로 서비스 중이며, 인스턴스의 IP를 사용해 인스턴스IP/8080 브라우저 접속
- 그리고 초기 패스워드를 입력
[unlock 이미지]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_unlock.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins unlock"></img><br/>


- 초기 설치는 Install suggested plugins를 사용
[jenkins_unlock 이미지]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_suggested_plugins_install.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins Install suggested plugins"></img><br/>


- Jenkins 관리자 계정 생성
[jenkins_create_First_admin_user]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_create_First_admin_user.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins admin creation"></img><br/>


- 인스턴스의 URL은 자동 입력되며, 서비스하고 있는 인스턴스의 IP로 진행
[jenkins_IP_conf]   

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_IP_conf.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins service URL"></img><br/>


- Jenkins 구성 완료
[jenkins_setup_complete]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_setup_complete.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins config complete"></img><br/>


- Jenkins 플러그인 관리 선택 및 설치
[Jenkins_manage_plugin]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/Jenkins_manage_plugin.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins plugin install"></img><br/>


- Jenkins 플러그인:설치가능에서 docker pipeline 검색 및 [Install without restart] 선택
[Jenkins_docker_pipeline_install]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/Jenkins_docker_pipeline_install.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins plugin : docker pipeline"></img><br/>


- Jenkins 플러그인 설치 후 메인페이지 돌아가기
[Jenkins_plugin_install_mainpage_back]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/Jenkins_plugin_install_mainpage_back.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins plugin : mainpage back"></img><br/>


- Jenkins 플러그인 gogs 설치 
[Jenkins_gogs_install]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/Jenkins_gogs_install.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="Jenkins plugin : gogs"></img><br/>





