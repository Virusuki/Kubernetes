# flaco 개요
- 클라우드 네이티브 런타임 보안 프로젝트인 falco는 쿠버네티스 위협 탐지 엔진
- CNCF에 합류한 최초의 런타임 보안 프로젝트
- 예기치 않은 애플리케이션 동작을 감지하고 런타임 시 위협에 대해 경고

## flaco 설치 - 전체 
```
curl -s https://falco.org/repo/falcosecurity-3672BA8F.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" | tee -a /etc/apt/sources.list.d/falcosecurity.list
apt-get update -y

apt-get -y install linux-headers-$(uname -r)

apt-get install -y falco
```

## 호스트에 falco 설정 정보 확인
- log_syslog: true 옵션은 /var/log/syslog에 falco 관련 로그를 함께 로깅한다는 의미
- rules_file 섹션에는 실행할 때 참조하는 룰의 위치가 명시됨 
```
# vi /etc/falco/falco.yaml
# rule 파일 목록
rules_file:
  - /etc/falco/falco.yaml
  - /etc/falco/falco_rules.local.yaml
  - /etc/falco/k8s_audit_rules.yaml
  - /etc/falco/rules.d
```

## flaco 룰 예제
- A shell is run in a container Rule
- Rule condition (룰 조건)
```
- macro: container  
condition: container.id != host  # 컨테이너 ID가 host 이면 안된다. 즉, 컨테이너로 보겠다.

- macro: spawned_process   
condition: evt.type = execve and evt.dir=<   # 이벤트 type이 execve 이여야하고, evt.dir 포함되어야 하며, 프로세스를 실행되는 내용이여야 한다.

- rule: run_shell_in_container
desc: a shell was spawned by a non-shell program in a container. Container entrypoints are excluded.
condition: container and proc.name = bash and spawned_process and proc.pname exists and not proc.pname in (bash, docker) 
#해당 조건 풀이, 컨테이너(==True)와 spawned_process(==True)이고, bash여야하고 proc.pname(parent 프로세스여야하고 고아 프로세스는 안된다). 
마지막으로 proc.pname(parent-name)이 bash or 도커는 안된다.(즉, 컨테이너 내에서 특정한 프로세스가 내부적으로 bash를 실행하면 그때 탐지하는 룰이다.)

output: "Shell spawned in a container other than entrypoint (user=%user.name container_id=%container.id
container_name=%container.name shell=%proc.name parent=%proc.pname cmdline=%proc.cmdline)“
priority: WARNING
```

## 간단한 로그 생성과 탐지
- python 이미지 컨테이너를 구성하고 패키지 매니저를 통해 vim 설치 
  - 솔루션은 제품을 운영하면서 업데이트를 하지않는게 일반적이다. 컨테이너를 배포한다는 것은 이미 안정적으로 run되는 컨테이너를 
    배포하고, 레지스트리에 올려놓고 다운로드해서 사용하는 형태인데, 쿠버네티스 안에서 패키지가 실행됐다는 것은 의심하는 사례로 본다.
```
kubectl run py --image=python:3.7 -- sleep infinity
kubectl exec py -- apt update
kubectl exec py -- apt install -y vim
```

- py pod가 할당된 노드 확인
```
kubectl get pod -o wide 
NAME   READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
py     1/1     Running   0          3m12s   10.40.0.10   namuk-04   <none>           <none>
```

- 생성된 py pod가 할당된 노드로 가서 falco 실행 및 로그 확인
```
# flaco  
cat /var/log/syslog | grep falco
```

- syslog에 falco를 grep 하면 관련 로그 확인이 가능
```
root@namuk-04:~# cat /var/log/syslog | grep falco
Dec 25 14:31:51 namuk-04 falco: Falco version 0.30.0 (driver version 3aa7a83bf7b9e6229a3824e3fd1f4452d1e95cb4)
Dec 25 14:31:51 namuk-04 falco: Falco initialized with configuration file /etc/falco/falco.yaml
Dec 25 14:31:51 namuk-04 falco: Loading rules from file /etc/falco/falco_rules.yaml:
Dec 25 14:31:51 namuk-04 falco: Loading rules from file /etc/falco/falco_rules.local.yaml:
Dec 25 14:31:51 namuk-04 falco: Loading rules from file /etc/falco/k8s_audit_rules.yaml:
Dec 25 14:31:52 namuk-04 kernel: [2181313.220859] falco: loading out-of-tree module taints kernel.
Dec 25 14:31:52 namuk-04 kernel: [2181313.221569] falco: module verification failed: signature and/or required key missing - tainting kernel
Dec 25 14:31:52 namuk-04 kernel: [2181313.222973] falco: driver loading, falco 3aa7a83bf7b9e6229a3824e3fd1f4452d1e95cb4
Dec 25 14:31:52 namuk-04 kernel: [2181313.224928] falco: adding new consumer 00000000c5a4424c
Dec 25 14:31:52 namuk-04 kernel: [2181313.224949] falco: initializing ring buffer for CPU 0
Dec 25 14:31:52 namuk-04 kernel: [2181313.233721] falco: CPU buffer initialized, size=8388608
Dec 25 14:31:52 namuk-04 kernel: [2181313.233722] falco: initializing ring buffer for CPU 1
Dec 25 14:31:52 namuk-04 kernel: [2181313.241641] falco: CPU buffer initialized, size=8388608
Dec 25 14:31:52 namuk-04 kernel: [2181313.241642] falco: starting capture
Dec 25 14:31:53 namuk-04 falco: Starting internal webserver, listening on port 8765
Dec 25 14:31:53 namuk-04 falco: 14:31:53.263826000: Notice Privileged container started (user=root user_loginuid=0 command=container:aa8f22e08c89 k8s_POD_csi-cephfsplugin-chcfl_rook-ceph_fffab893-49dc-4136-a1c2-15c0bc91e706_0 (id=aa8f22e08c89) image=k8s.gcr.io/pause:3.5)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.435128000: Notice Privileged container started (user=root user_loginuid=0 command=container:6fc91e38e92f k8s_driver-registrar_csi-rbdplugin-qf74m_rook-ceph_2d45ea37-fd23-4f1a-ad50-5104b281a57f_0 (id=6fc91e38e92f) image=k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.3.0)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.467803630: Notice Privileged container started (user=root user_loginuid=0 command=container:75ddffc9ea5d k8s_driver-registrar_csi-cephfsplugin-chcfl_rook-ceph_fffab893-49dc-4136-a1c2-15c0bc91e706_0 (id=75ddffc9ea5d) image=k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.3.0)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.528355583: Notice Privileged container started (user=root user_loginuid=0 command=container:f696654343cd k8s_csi-rbdplugin_csi-rbdplugin-qf74m_rook-ceph_2d45ea37-fd23-4f1a-ad50-5104b281a57f_0 (id=f696654343cd) image=quay.io/cephcsi/cephcsi:v3.4.0)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.596012970: Notice Privileged container started (user=root user_loginuid=0 command=container:16378d22a7a7 k8s_liveness-prometheus_csi-rbdplugin-qf74m_rook-ceph_2d45ea37-fd23-4f1a-ad50-5104b281a57f_0 (id=16378d22a7a7) image=quay.io/cephcsi/cephcsi:v3.4.0)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.596012970: Notice Privileged container started (user=root user_loginuid=0 command=container:1ec6f3ad71f4 k8s_csi-cephfsplugin_csi-cephfsplugin-chcfl_rook-ceph_fffab893-49dc-4136-a1c2-15c0bc91e706_0 (id=1ec6f3ad71f4) image=quay.io/cephcsi/cephcsi:v3.4.0)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.596012970: Notice Privileged container started (user=root user_loginuid=0 command=container:af11b6901b4b k8s_liveness-prometheus_csi-cephfsplugin-chcfl_rook-ceph_fffab893-49dc-4136-a1c2-15c0bc91e706_0 (id=af11b6901b4b) image=quay.io/cephcsi/cephcsi:v3.4.0)
Dec 25 14:31:53 namuk-04 falco: 14:31:53.958952077: Notice Privileged container started (user=root user_loginuid=0 command=container:bd3af4f45ef2 k8s_osd_rook-ceph-osd-0-6ff96fdbcf-x8qsw_rook-ceph_efa96c22-e757-49f7-9aad-9df7add7554b_2 (id=bd3af4f45ef2) image=quay.io/ceph/ceph:v16.2.6)
Dec 25 14:31:54 namuk-04 falco: 14:31:54.368587689: Notice Privileged container started (user=<NA> user_loginuid=0 command=container:d29d3942b4d7 k8s_POD_rook-ceph-osd-0-6ff96fdbcf-x8qsw_rook-ceph_efa96c22-e757-49f7-9aad-9df7add7554b_0 (id=d29d3942b4d7) image=k8s.gcr.io/pause:3.5)
Dec 25 14:31:55 namuk-04 falco: 14:31:55.092261181: Notice Privileged container started (user=<NA> user_loginuid=0 command=container:5541a2b42b43 k8s_POD_kube-proxy-wnntk_kube-system_4e250fad-9efb-409c-ad58-ab58e37a9430_2 (id=5541a2b42b43) image=k8s.gcr.io/pause:3.5)
Dec 25 14:31:55 namuk-04 falco: 14:31:55.219721363: Notice Privileged container started (user=<NA> user_loginuid=0 command=container:a237ea5f37d1 k8s_POD_weave-net-6jsp9_kube-system_b6ac0915-be9d-42ce-9c66-dab34adbe50e_1 (id=a237ea5f37d1) image=k8s.gcr.io/pause:3.5)
Dec 25 14:31:55 namuk-04 falco: 14:31:55.281262139: Notice Privileged container started (user=<NA> user_loginuid=0 command=container:a32d19994e4a k8s_weave_weave-net-6jsp9_kube-system_b6ac0915-be9d-42ce-9c66-dab34adbe50e_2 (id=a32d19994e4a) image=ghcr.io/weaveworks/launcher/weave-kube:2.8.1)
Dec 25 14:31:55 namuk-04 falco: 14:31:55.402999206: Notice Privileged container started (user=<NA> user_loginuid=0 command=container:793fcfa94408 k8s_weave-npc_weave-net-6jsp9_kube-system_b6ac0915-be9d-42ce-9c66-dab34adbe50e_1 (id=793fcfa94408) image=ghcr.io/weaveworks/launcher/weave-npc:2.8.1)
Dec 25 14:31:55 namuk-04 falco: 14:31:55.716222955: Notice Privileged container started (user=<NA> user_loginuid=0 command=container:e30818e7ebf3 k8s_POD_csi-rbdplugin-qf74m_rook-ceph_2d45ea37-fd23-4f1a-ad50-5104b281a57f_0 (id=e30818e7ebf3) image=k8s.gcr.io/pause:3.5)
Dec 25 14:40:48 namuk-04 falco: 14:40:48.524565659: Error Package management process launched in container (user=root user_loginuid=-1 command=apt update container_id=eda4e44edabe container_name=k8s_py_py_default_549185c2-9193-4606-b9f5-cb4565fb70bd_0 image=python:3.7)
Dec 25 14:40:54 namuk-04 falco: 14:40:54.615781734: Error Package management process launched in container (user=root user_loginuid=-1 command=apt install -y vim container_id=eda4e44edabe container_name=k8s_py_py_default_549185c2-9193-4606-b9f5-cb4565fb70bd_0 image=python:3.7)


```




