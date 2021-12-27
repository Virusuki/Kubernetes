# Multi-container(멀티 컨테이너) 개요
- 하나의 파드안에 다수의 컨테이너를 사용합니다.
   - 1개의 파드안에 컨테이너 2개이상을 배치하는 경우, 애플리케이션끼리 묶어서 배포하는 것이 아닌
     메인 컨테이너를 기본으로 배치하고, 2번재 부터의 컨테이너는 보조적인 역할을 위해서 올라가게 됩니다.
- 하나의 파드를 사용하는 경우 같은 네트워크 인터페이스와 IPC, 볼륨등을 공유할 수 있습니다.
- 하나의 파드(멀티 컨테이너일 때)는 효율적으로 통신하여 데이터의 지역성을 보장하고, 여러 개의 응용프로그램이
  결합된 형태로 하나의 파드를 구성할 수 있습니다.

- pod-multi-container.yaml (예제)

```
apiVersion: v1
kind: Pod
metadata:
  name: two-containers
spec:

  restartPolicy: Never

  volumes:
  - name: shared-data
    emptyDir: {}
 
  containers:        # 쿠버네티스에서는 container's' 로 끝나면 복수형으로 의미합니다. 아래는 하나의 리스트 요소로 받아들일 수 있습니다.

  - name: nginx-container  # 앞에 리스트의 시작부분'-' 문자열은 다음 '-'가 나오면 첫번째 리스트 요소입니다.
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html

  - name: debian-container
    image: debian
    volumeMounts:
    - name: shared-data
      mountPath: /pod-data
    command: ["/bin/sh"]
    args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
``` 

- 하나의 파드안에서 nginx와 redis 이미지를 실행하는 예제 yaml 파일

``` 
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: two-containers
spec:
  containers:
  - name: nginx-container
    image: nginx:1.21.3
  - name: redis-container
    image: redis:6.2.6
EOF
``` 
    
- 하나의 파드안에 두 개 이상의 컨테이너가 올라가 있는 환경에서 로그 확인 or exec 명령을 실행할 특정 컨테이너 선택할 경우

``` 
# kubectl logs two-containers nginx-container
# kubectl exec -it two-containers -c nginx-container -- bash

``` 
