# 쿠버네티스 설치 
- 설치 URL: https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

## 런타임 설치
- 도커 및 kubelet, kubeadm, kubectl 설치 
```
cat <<EOF > kube_install.sh
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl docker.io
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
EOF
bash kube_install.sh
```

## cgroup 드라이버 구성
- kubelet은 systemd를 사용하니, cgrupd에서 매칭 시켜줘야 함

```
cat <<EOF > /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF

service docker restart
```

########################


- 
docker info 

Reference:
- https://stackoverflow.com/questions/43794169/docker-change-cgroup-driver-to-systemd
- https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
