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

```

- 다음 명령을 실행해 bitnami 저장소를 helm 목록에 추가한 뒤 업데이트 진행
```
# helm repo update
```

- 다음 명령을 실행해 bitnami 저장소를 helm 목록에 추가한 뒤 업데이트 진행
```
# helm search repo bitnami (bitnami 라는 키워드 검색)


```










Reference:
- https://helm.sh/ko/docs/intro/install/
