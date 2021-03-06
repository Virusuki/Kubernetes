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

: 만약 재시작이 안될 경우, "설치가 끝나고 실행중인 작업이 없으면 Jenkins 재시작" 버튼 클릭

- 재부팅 후, 다시 젠킨스 로그인 시작!
- 파이프라인을 만들기 위해, job을 생성
[jenkins_job_pipeline]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_job_pipeline.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_job_pipeline"></img><br/>


- github에 있는 파일의 변동사항이 발생했을때마다, 젠킨스가 정보를 받아서 빌드, 테스트, registry(harbor)에 보냄
- 일련의 작업을 위한 jenkins(젠킨스)에서 '새로운 item'의 파이프라인 생성
[jenkins_pipeline_select]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_pipeline_select.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_pipeline_select"></img><br/>



- 어느 repository에서  가져올지 관련한 정보를 기입
- (gogs에서 받아올수 있도록)"GitHub hook trigger for GITScm polling"를 선택 
- 하단의 (스크립트를 github에서 불러오도록)Pipeline script from SCM 선택
   - SCM -> Git 선택
(Failed to connect to repository : Command "git ls-remote -h -- http://172.30.4.108:3000/gogs/flask-example HEAD" returned status code 128:
stdout: stderr: fatal: Authentication failed for 'http://172.30.4.108:3000/gogs/flask-example/') 라는 오류가 발생하는데, 정상입니다. 이유는 (gogos의 인증정보가 없어서 오류가 발생함)

[jenkins_pipeline_definition]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_pipeline_definition.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_pipeline_definition"></img><br/>


- 인증정보 add 
- gogs의 인증정보를 설정하고 저장
- gogs의 ID/PW 기입
[jenkins_gogs_credit_info]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_gogs_credit_info.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_pipeline_definition"></img><br/>



- 설정한 gogs 인증정보를 선택하면 오류 사라짐
- ID/PW로 접속하면 정보를 볼 수 있음
[jenkins_credentials_select]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_credentials_select.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_credentials_select"></img><br/>


- Branch specifier (blank for 'any') & script path 설정
- Branch는 github와 같이 기본 master로 설정
- script path는 jenkinsfile 파일 선택
   - jenkinsfile은 gogs에 jenkins 빌드 및 레지스트리 업로드 정보 포함
- 저장 및 젠킨스의 파이프라인 구성은 끝

[jenkins_branch_scriptpath_save]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_branch_scriptpath_save.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_branch_scriptpath_save"></img><br/>



- Gogs의 변경사항이 발생할 때, jenkins에 알려주기 위해, gogs에서 설정탭을 클릭
[jenkins_gogs_설정]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_gogs_%EC%84%A4%EC%A0%95.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_gogs_설정"></img><br/>



- Gogs의 설정탭 -> Webhooks 페이지 이동
- Webhooks은 어떤 이벤트가 발생했을 때, gogs에서 특정한 애플리케이션을 알림 역할
- gogs 선택
[jenkins_webhook_gogs_select]
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_webhook_gogs_select.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_webhook_gogs_select"></img><br/>



- webhook 추가 설정 
- 페이로드 URL은 jenkins로 보내기 위해, jenkins의 페이로드 URL은 다음과 같이 입력
- IP:port/gogs-webhook/?job=flask-example-docker-pipeline
- (gogs의 IP:Port 작성 gogs-webhook은 정해져있음, /?job=flask-example-docker-pipeline은 젠킨스의 생성한 파이프라인 name)
- http://10.0.0.1:8080/github-webhook <-- 만약 github에 보낼경우
- 마지막으로 추가 버튼을 누름
[jenkins_webhook_add]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_webhook_add.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_webhook_add"></img><br/>



- jenkinsfile을 수정함 (jenkinsfile은 맨 하단에 참조)
[gogs 설치 및 셋팅은 https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/gogos(%EA%B2%BD%EB%9F%89%ED%99%94%EB%90%9C%20gitlab).md 를 참조]
- jenkinsfile에서 이미지를 레지스트리에 push하기 위해서 harbor-cred를 만들어야 함
- harbor-cred은 harbor에 접속할 수 있는 인증정보이다. (젠킨스에서 생성이 필요)
- jenkins [dashboard] -> [Jenkins 관리] -> [Manage Credentials] 으로 진입
- 생성한 Credentials의 Jenkins를 클릭 -> Global credentials를 클릭하면 생성된 gogs-cred이 나옴
[jenkins_harbor_credentials]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_harbor_credentials.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_harbor_credentials"></img><br/>


- harbor cred 생성을 위해 add Credentials 클릭 후 추가
[jenkins_add_cred_harbor]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_add_cred_harbor.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_add_cred_harbor"></img><br/>



- harbor 인증서 만들기 위해 설정 및 ok 버튼 클릭
[jenkins_harbord_add_cred_config_complete]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_harbord_add_cred_config_complete.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_harbord_add_cred_config_complete"></img><br/>


- harbor 인증서 생성 확인
[jenkins_harbor_cred_생성완료]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_harbor_cred_%EC%83%9D%EC%84%B1%EC%99%84%EB%A3%8C.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_harbor_cred_생성완료"></img><br/>


- gogs의 jenkinsfile은 다음과 같아야 함
[jenkins_gogs_jenkinsfile_config]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_gogs_jenkinsfile_config.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_gogs_jenkinsfile_config"></img><br/>


- [gogs의 앱(명칭)메인] -> [설정 탭] 이동 -> [Webhooks] 녹색 체크 확인
[jenkins_gogs_webhook_green_status]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_gogs_webhook_green_status.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_gogs_webhook_green_status"></img><br/>


- Jenkins에서 생성한 파이프라인 변경사항 감지 확인
- 만약, 젠킨스에서 build가 안될 경우 build now 클릭
[jenkins_gogs의 변경사항 감지확인]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_gogs%EC%9D%98%20%EB%B3%80%EA%B2%BD%EC%82%AC%ED%95%AD%20%EA%B0%90%EC%A7%80%ED%99%95%EC%9D%B8.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_gogs의 변경사항 감지확인"></img><br/>

- harbor 브라우저 접속
- [Projects] -> [admin 계정] -> [Repositories]에서 push된 이미지 확인
[jenkins_harbor_image]

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Jenkins_img/jenkins_harbor_image.PNG" width="800px" height="400px" title="px(픽셀) 크기 설정" alt="jenkins_harbor_image"></img><br/>


- 이로써, [Gogs] -> [젠킨스 파이프라인] -> [harbor]에 업로드 하는데 까지 CI(Continuous Integration)를 구성 테스트 완료!!




---
jenkinsfile 젠킨스 파일 수정 
- 10.0.2.7 IP를 인스턴스의 IP로 수정 필요
```
node {
     stage('Clone repository') {
         checkout scm
     }
     stage('Build image') {
         app = docker.build("10.0.2.7/admin/flask-example")
         
     }
     stage('Push image') {
         docker.withRegistry('https://10.0.2.7', 'harbor-cred') {
             app.push("${env.BUILD_NUMBER}")
             app.push("latest")
         }
     }
}

stage('Build image') {
  app = docker.build("10.0.2.7/admin/flask-example")
}

stage('Push image') {
  docker.withRegistry('https://10.0.2.7', 'harbor-cred') 
  {
     app.push("${env.BUILD_NUMBER}")
     app.push("latest")
  }
}
```
