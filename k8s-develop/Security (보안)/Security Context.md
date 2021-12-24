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
