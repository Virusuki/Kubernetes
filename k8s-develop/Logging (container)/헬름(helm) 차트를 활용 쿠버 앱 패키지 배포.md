# helm 소개 및 설치
- helm은 쿠버네티스 애플리케이션 관리를 지원하는 도구
- 복잡한 쿠버네티스 애플리케이션 정의, 설치 및 업그레이드하는데 도움
- 복잡한 구조의 구조의 애플리케이션을 직접 구성하는 대신 helm에 정의되어 있는 내용을 사용해 쉽게 설치/삭제
- helm은 CNCF의 프로젝트이며 helm 커뮤니티에서 유지 관리


## helm의 4가지 특징
- 복잡성 관리: 헬름 차트는 복잡한 애플리케이션도 기술하고 반복적으로 설치 제공
- 쉬운 업데이트: 커스텀 후크와 in-place 업그레이드 지원해 업데이트의 고통 경감
- 간단한 공유: 차트는 공용 or 개인 서버에서 쉽게 버전관리, 공유 및 호스팅
- 롤백: helm 롤백을 사용하면 이전 버전의 release로 쉽게 롤백

### 헬름 3버전 아키텍처 구성
주로 helm 3버전을 사용하며, 별도의 helm 서버를 운영하지 않고, Helm Client(helm)를 구성해서 바로 배포할 수 있도록 구성
helm client는 Chart repository(yaml파일들이 많음)에 접근하여 helm client에 helm install 명령만 하면 kubernetes API server에 배포가 됨
helm client가 구성되는 곳은 kubectl에 구성된 곳이어야 됨.

## helm Install
- 호스트에 구성된 kubectl과 인증 설정이 있어야 정상적 동작

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm -h
```

- default 스토리지클래스로 rook-ceph-block을 사용하도록 설정
```
kubectl patch storageclass rook-ceph-block -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

- rook-ceph-block (default)가 되어있어야 함
```
root@namuk-01:~# kubectl get sc
NAME                        PROVISIONER                  RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
namuk-rook-ceph-block       rook-ceph.rbd.csi.ceph.com   Delete          Immediate           true                   26d
rook-ceph-block (default)   rook-ceph.rbd.csi.ceph.com   Delete          Immediate           true                   26d
```

## helm 차트 repository 초기화
- helm은 외부에서 정의된 yaml 파일을 내려 받아 쿠버네티스 애플리케이션 배포
- apt, yum과 같이 저장소를 별도로 두고 있음
- 외부 저장소를 helm repo add 명령으로 추가

- 다음 명령을 실행해 bitnami 저장소를 helm 목록에 추가한 뒤 업데이트 진행
```
# helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

- 다음 명령을 실행해 bitnami 저장소를 helm 목록에 추가한 뒤 업데이트 진행
- 리자피토리 리스트에 등록된 repo에 대한 내용을 업데이트함
```
# helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈
```

- 다음 명령을 실행해 bitnami 저장소를 helm 목록에 추가한 뒤 업데이트 진행(bitnami 라는 키워드 검색)
```
# helm search repo bitnami 
```

- helm 차트로 mysql 배포
```
- 네임스페이스 생성
# kubectl create ns mysql  

- mysqlname 라는 고유이름을 지어줌
# helm install mysqlname bitnami/mysql -n mysql
```

- mysqlname 이름으로 배포 확인
```
root@namuk-01:~# helm install mysqlname bitnami/mysql -n mysql
NAME: mysqlname
LAST DEPLOYED: Thu Dec 30 08:58:42 2021
NAMESPACE: mysql
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: mysql
CHART VERSION: 8.8.18
APP VERSION: 8.0.27

** Please be patient while the chart is being deployed **
...

kubectl run mysqlname-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mysql:8.0.27-debian-10-r63 --namespace mysql --command -- bash
...
kubectl get secret --namespace mysql mysqlname -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
```

- 아래의 그대로 복사하면 mysql namespace에 정보를 얻을 수 있음 
```
# kubectl get secret --namespace mysql mysqlname -o jsonpath="{.data.mysql-root-password}" | base64 --decode) 패스워드가 나옴

```

- 배포된 mysql 확인 및 패스워드 확인
```
# kubectl get pod -n mysql
# kubectl get secret --namespace mysql mysqlname -o jsonpath="{.data.mysql-root-password}" | base64 --decode
```

- mysql 접속
```
# kubectl -n mysql exec -it mysqlname-0 -- mysql -u root -p
```

- 헬름 리스트, 상태 확인 및 배포된 패키지 삭제
```
# helm list -n mysql
# helm status mysqlname -n mysql
# helm uninstall mysqlname -n mysql
```

- 헬름 리파지토리 삭제
```
# helm repo remove bitnami
```

## helm 차트 생성/실행

### 헬름 차트 구성요소
- 쿠버네티스에서 애플리케이션을 배포할 때 필요한 기본적인 리소스를 확인
- 3-티어로 구성된 해당 그림은 각 계층마다 디플로이먼트와 서비스를 포함
- 추가적으로 Configmap과 secret등을 사용해 추가 설정할 수 있도록 구성되어 있음
- 헬름 차트 내에서도 이러한 정보들은 yaml 파일로 정의

- helm create
```
# helm create mychart
```

- tree 설치 명령어
```
# sudo apt update && sudo apt install tree -y
# tree mychart
```
```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```


- templates 디렉토리
   - 템플릿 디렉토리는 서비스 하는데 필요한 자원들의 yaml 파일의 집합
   - deployment, service, hpa 등 구성됨
   - 템플릿 파일은 go 템플릿 렌더링엔진에서 읽을 수 있는 변수 형태로 구성
   - {{}}기호안의 내용들은 템플릿이 구성될 때 외부의 값을 통해 코드가 실행되면서 결정되는 내용

- 템플릿 자료 확인
```
# head -n 20 mychart/templates/deployment.yaml
```

- 템플릿 자료 확인
```
# cat mychart/templates/service.yaml
```

- 서비스에 value 전달하기 
- -values.yaml 파일로 전달
```
# vi mychart/values.yaml
```
- values.yaml에서 다음과 같이 수정 라인
```
image:
  repository: httpd
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "2.4"

service:
  type: LoadBalancer
  port: 80

```

- 헬름차트 배포

```
helm install mychart ./mychart/
```

- namespace를 별도로 주지않았기 떄문에 namespace를 정의
```
NAME: mychart
LAST DEPLOYED: Thu Dec 30 11:16:36 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get --namespace default svc -w mychart'
  export SERVICE_IP=$(kubectl get svc --namespace default mychart --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo http://$SERVICE_IP:80
```

- 서비스 목록을 보면 확인할 수 있음
```
root@ubuntu-kube-master1:~# kubectl get svc
NAME                         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
mychart                      LoadBalancer   10.108.162.167   <pending>     80:32383/TCP     4m3s
```

- 헬름 mychart 삭제
```
# helm uninstall mychart
```


Reference:
- https://helm.sh/ko/docs/intro/install/
- https://docs.vmware.com/en/VMware-Application-Catalog/services/tutorials/GUID-create-first-helm-chart-index.html
