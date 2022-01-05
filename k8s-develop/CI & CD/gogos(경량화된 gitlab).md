# gogos (경량화된 gitlab)
- gogos의 최소 사양은 4 CPU(추천 8)

## gogos 설치 
```
mkdir ~/gogs
cd ~/gogs 
wget https://gist.githubusercontent.com/ahromis/4ce4a58623847ca82cb1b745c2f83c82/raw/31e8ced3d7e08c602a1c0ca8994c063994971c7f/docker-compose.yml

vim docker-compose.yml
```   


- 데이터베이스의 ID와 비밀번호 설정
```
version: '2'
services:
    postgres:
      image: postgres:9.5
      restart: always
      environment:
       - "POSTGRES_USER=gogs"
       - "POSTGRES_PASSWORD=test1234"
       - "POSTGRES_DB=gogs"
      volumes:
       - "db-data:/var/lib/postgresql/data"
      networks:
       - gogs
    gogs:
      image: gogs/gogs:latest
      restart: always
      ports:
       - "10022:22"
       - "3000:3000"
      links:
       - postgres
      environment:
       - "RUN_CROND=true"
      networks:
       - gogs
      volumes:
       - "gogs-data:/data"
      depends_on:
       - postgres

networks:
    gogs:
      driver: bridge

volumes:
    db-data:
      driver: local
    gogs-data:
      driver: local
```

- docker-compose 실행
```
docker-compose -f docker-compose.yml up -d
```

- 아래의 명령어를 통해 포트 확인(3000) 서비스 실행
- 인스턴스 IP/Port로 서비스 접속
```
docker ps -a
```

- Gogs 첫 화면 UI

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/gogos_UI.PNG" width="670px" height="750px" title="px(픽셀) 크기 설정" alt="Gogs UI"></img><br/>   


- Gogs DB 설정과 관리자계정 설정
- 애플리케이션 일반 설정에서 도메인은 인스턴스 IP로 설정 
- 애플리케이션 URL : http://인스턴스IP:3000/

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/gogos_env_setting.PNG" width="570px" height="720px" title="px(픽셀) 크기 설정" alt="Gogs setting"></img><br/>


- 

- Gogs 저장소 생성을 위한 (새저장소)+ 클릭 및 새저장소 생성

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/gogs_new-repository.PNG" width="670px" height="360px" title="px(픽셀) 크기 설정" alt="Gogs setting"></img><br/>



- Gogs 새저장소 셋팅 및 저장소 만들기

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/gogs_repository_creation.PNG" width="650px" height="720px" title="px(픽셀) 크기 설정" alt="Gogs setting"></img><br/>


- github와 같은 gogs gitlab 확인

<img src="https://github.com/Virusuki/Kubernetes/blob/main/k8s-develop/CI%20%26%20CD/files/img/gogs_github_flask.PNG" width="7000px" height="720px" title="px(픽셀) 크기 설정" alt="Gogs setting"></img><br/>

