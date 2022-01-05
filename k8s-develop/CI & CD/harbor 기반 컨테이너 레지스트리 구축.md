# Harbor란?
- CNCF 졸업 프로젝트 오픈 소스 레지스트리
- 정책 및 역할 기반 엑세스 제어를 제공
- Artifact(이미지)를 보호하고, 이미지 스캔, 취약성 확인, 이미지를 신뢰할 수 있도록 서명
- 웹 대시보드 제공

## Harbor install
```
# docker 설치
apt update  && apt install -y docker.io

# 도커 컴포즈 설치
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

docker rm --force `docker ps -a -q`
docker volume rm --force `docker volume ls -q`
```


- harbor 컨테이너 레지스트리 설치
- 스크립트를 사용해서 설치 진행(harbor io)
```
wget https://gist.githubusercontent.com/kacole2/95e83ac84fec950b1a70b0853d6594dc/raw/ad6d65d66134b3f40900fa30f5a884879c5ca5f9/harbor.sh
bash harbor.sh
```

- 도메인이 없으면, IP 선택
```
1) IP
2) FQDN
Would you like to install Harbor based on IP or FQDN?
> 1번 선택
```

- harbor는 https를 통신
- HTTPS 통신을 위한 인증서를 구성하고 설정하여 설치를 진행해야 한다. 
- 다음 스크립트를 구성하고 실행해 CA와 Harbor에서 사용할 Certificate를 생성한다.

```
cd ~
mkdir pki
cd pki

# ca 키와 인증서 생성
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
    -out ca.crt \
    -keyout ca.key \
    -subj "/CN=ca"

# harbor server 키와 인증서 생성
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr -subj "/CN=harbor-server"
openssl x509 -req -in server.csr -CA ca.crt \
                                  -CAkey ca.key \
                                  -CAcreateserial -out server.crt -days 365

# 키와 인증서 복제
mkdir -p /etc/docker/certs.d/server
cp server.crt /etc/docker/certs.d/server/  # harbor.yaml 파일 내부의 certificate
cp server.key /etc/docker/certs.d/server/  # harbor.yaml 파일 내부의 private_key 
cp ca.crt /etc/docker/certs.d/server/

cp ca.crt /usr/local/share/ca-certificates/harbor-ca.crt
cp server.crt /usr/local/share/ca-certificates/harbor-server.crt
update-ca-certificates
```

- harbor.yaml 템플릿을 사용해 harbor의 설정을 구성
```
cd ~/harbor
cp harbor.yml.tmpl harbor.yml
vim harbor.yml
```

- harbor.yaml 파일의 내부에서 hostname, 
```
# 현재 인스턴스의 IP
hostname: 10.0.2.7

# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 80

# https related config
https:
  # https port for harbor, default is 443
  port: 443
  # The path of cert and key files for nginx
  certificate: /etc/docker/certs.d/server/server.crt
  private_key: /etc/docker/certs.d/server/server.key
...
harbor_admin_password: Test1234   # Harbor의 비밀번호를 바꿔주는 것을 권장. 외부 공격으로부터 패스워드 변경 방지
```


- 마지막으로 prepare 및 install.sh 스크립트를 실행
- prepare 스크립트는 이미지를 준비하고 인증서 파일을 위한 설정을 구성
- install.sh 파일은 도커 컴포즈를 사용해 harbor 실행에 필요한 컨테이너들을 배포   

```
./prepare
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
./install.sh
```


- harbor 안전하지 않음 버튼 클릭

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/harbor%20UI1.PNG" width="660px" height="520px" title="px(픽셀) 크기 설정" alt="harbor init UI"></img><br/>   


- harbor login UI
- admin / password는 harbor.yaml에서 설정한 비밀번호

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Harbor_login.PNG" width="660px" height="520px" title="px(픽셀) 크기 설정" alt="harbor login UI"></img><br/>   



- harbor New Project 생성
- 스토리지 할당량의 -1은 제한이 없는 것
<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/harbor_newproject.PNG" width="700px" height="550px" title="px(픽셀) 크기 설정" alt="harbor New project"></img><br/>
    


- harbor 프로젝트를 생성하고 admin 프로젝트로 가면 해당 기능을 사용할 수 있는 user를 지정할 수 있는 기능이 있으며, 
  프로젝트 단위로 권한 부여해 엑세스할 수 있는 사용자 

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/Harbor_User_privilege.PNG" width="785px" height="450px" title="px(픽셀) 크기 설정" alt="harbor user privilege"></img><br/>


- docker CLI를 활용한 Harbor 사용
   - 도커 CLI에서, admin 사용자를 사용해 이미지 업로드를 위해 로그인 진행
```
docker login 127.0.0.1 -u admin -p Test1234
```
