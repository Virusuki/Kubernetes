# 쿠버네티스 CIS 벤치마크
- CIS는 클라우드에서 준수해야하는 다양한 보안 점검 리스트를 benchmark로 제공하며, 사이트를 통해 상세히 명시된 pdf 파일을 다운로드할 수 있다.
  - https://www.cisecurity.org/benchmark/kubernetes/

## kube-bench?
- aquasecurity에서는 쿠버네티스에서 사용할 수 있는 kube-bench 오픈소스를 구성하여 사용자들에게 쿠버네티스에 설정 취약성을 파악할 수 있도록 구성
- 다양한 플랫폼에서 컴파일된 파일을 구성하도록 제공
  - https://github.com/aquasecurity/kube-bench/releases

## kube-bench 설치 및 클러스터 진단 

- kube-bench release 파일 다운로드
```
wget https://github.com/aquasecurity/kube-bench/releases/download/v0.6.3/kube-bench_0.6.3_linux_amd64.tar.gz 
tar -xf kube-bench_0.6.3_linux_amd64.tar.gz
sudo mv kube-bench /usr/bin/ # 설치 완료
```

- Github 다운로드
```
git clone https://github.com/aquasecurity/kube-bench 
cd kube-bench
```

- Master 노드 점검
```
kube-bench --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml run --targets=master
```
- Master 노드 점검 결과

```
etcd 관련 부분 Fail이 발생함. 원인은 권장사항에 etcd만 접근이 가능하도록 하라는 사항이 있다.!

[INFO] 1 Master Node Security Configuration
[INFO] 1.1 Master Node Configuration Files
[PASS] 1.1.1 Ensure that the API server pod specification file permissions are set to 644 or more restrictive (Automated)
[PASS] 1.1.2 Ensure that the API server pod specification file ownership is set to root:root (Automated)
[PASS] 1.1.3 Ensure that the controller manager pod specification file permissions are set to 644 or more restrictive (Automated)
[PASS] 1.1.4 Ensure that the controller manager pod specification file ownership is set to root:root (Automated)
[PASS] 1.1.5 Ensure that the scheduler pod specification file permissions are set to 644 or more restrictive (Automated)
[PASS] 1.1.6 Ensure that the scheduler pod specification file ownership is set to root:root (Automated)
[PASS] 1.1.7 Ensure that the etcd pod specification file permissions are set to 644 or more restrictive (Automated)
[PASS] 1.1.8 Ensure that the etcd pod specification file ownership is set to root:root (Automated)
[WARN] 1.1.9 Ensure that the Container Network Interface file permissions are set to 644 or more restrictive (Manual)
[WARN] 1.1.10 Ensure that the Container Network Interface file ownership is set to root:root (Manual)
[PASS] 1.1.11 Ensure that the etcd data directory permissions are set to 700 or more restrictive (Automated)
[FAIL] 1.1.12 Ensure that the etcd data directory ownership is set to etcd:etcd (Automated)  <--- etcd 관련 부분 Fail이 발생!!!!!!!!!!!!!!


== Remediations master ==
1.1.9 Run the below command (based on the file location on your system) on the master node.
For example,
chmod 644 <path/to/cni/files>

1.1.10 Run the below command (based on the file location on your system) on the master node.
For example,
chown root:root <path/to/cni/files>

1.1.12 On the etcd server node, get the etcd data directory, passed as an argument --data-dir,
from the below command:
ps -ef | grep etcd
Run the below command (based on the etcd data directory found above).
For example, chown etcd:etcd /var/lib/etcd

```



- Worker 노드 점검
```

kube-bench --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml run --targets=node

```
