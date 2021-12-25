# 시큐리티 컨텍스트

## Pod에 시큐리티 컨텍스트 설정하기
  - Pod에 UID를 설정하면 모든 컨테이너에 적용됨
    - 컨테이너 UID/GID 설정하기
    - 권한상승 제한하기
      - allowPrivilegeEscalation: false 로 설정하게 되면 privilege를 제한함

```
$ kubectlexec -it security-context-example /bin/bash
/ $ id
uid=1000 gid=3000 groups=2000
/ $ ps   # 프로세스의 권한이 1000으로 실행되는 것을 확인할 수 있음
PID   USER     TIME  COMMAND
    1 1000      0:00 sleep 1h
    8 1000      0:00 sh
   15 1000      0:00 ps
```

```
/data/demo $ echo hello > testfile # 2000의 권한을 가지고 있기 떄문에 디렉토리를 사용할 수 
/data/demo $ ls -al
total 12
drwxrwsrwx    2 root     2000          4096 Dec 25 00:05 .
drwxr-xr-x    3 root     root          4096 Dec 25 00:00 ..
-rw-r--r--    1 1000     2000             6 Dec 25 00:05 testfile
```

```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-example
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: sec-ctx-ex
    image: busybox
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```


## 컨테이너 내부에 새로운 User를 사용하면 그 설정이 우선순위가 된다.
  - 컨테이너 UID/GID 설정하기
  - 아래의 yaml 예제에서 Pod 전체를 설정할 수 있고, 컨테이너 자체에서도 설정할 수 있다.   


```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-example2
spec:
  securityContext:
    runAsUser: 1000  # uid
  containers:
  - name: sec-context-ex2
    image: busybox
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext:   # securityContext가 겹쳐 있으면 컨테이너가 우선으로 설정됨
      runAsUser: 2000
      allowPrivilegeEscalation: false  
```

## Capabilities 적용
  - Capabilities를 적용하면 리눅스 커널에서 사용할 수 있는 권한을 추가 설정이 가능하다.
    - date +%T -s "08:00:00" 명령은 SYS_TIME 커널 권한이 있어야만 가능하다
   
```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-example3
spec:
  containers:
  - name: sec-ctx-ex3
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```


