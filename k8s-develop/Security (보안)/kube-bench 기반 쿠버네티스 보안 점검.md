# 쿠버네티스 CIS 벤치마크
- CIS는 클라우드에서 준수해야하는 다양한 보안 점검 리스트를 benchmark로 제공하며, 사이트를 통해 상세히 명시된 pdf 파일을 다운로드할 수 있다.
  - https://www.cisecurity.org/benchmark/kubernetes/

## kube-bench?
- aquasecurity에서는 쿠버네티스에서 사용할 수 있는 kube-bench 오픈소스를 구성하여 사용자들에게 쿠버네티스에 설정 취약성을 파악할 수 있도록 구성
- 다양한 플랫폼에서 컴파일된 파일을 구성하도록 제공
  - https://github.com/aquasecurity/kube-bench/releases

## kube-bench 설치 및 클러스터 진단 

- 릴리즈 파일 다운로드
```
wget https://github.com/aquasecurity/kube-bench/releases/download/v0.6.3/kube-bench_0.6.3_linux_amd64.tar.gz 
tar -xf kube-bench_0.6.3_linux_amd64.tar.gz
sudo mv kube-bench /usr/bin/ # 설치 완료
```

- 깃헙 프로젝트 다운로드
```
git clone https://github.com/aquasecurity/kube-bench 
cd kube-bench
```
