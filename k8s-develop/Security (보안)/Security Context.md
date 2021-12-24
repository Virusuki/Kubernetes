# Pod에 시큐리티 컨텍스트 설정하기
  - Pod에 UID를 설정하면 모든 컨테이너에 적용됨
    - 컨테이너 UID/GID 설정하기
    - 권한상승 제한하기
      - allowPrivilegeEscalation: false 로 설정하게 되면 privilege를 제한함

```
$ kubectlexec -it security-context-example /bin/bash
/ $ id
uid=1000 gid=3000 groups=2000
/$ ps -eaf
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
  - name: sec-ctx-demo
    image: busybox
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```


- 컨테이너 내부에 새로운 User를 사용하면 그 설정이 우선순위가 된다.
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

- Capabilit


