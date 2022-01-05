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
```
