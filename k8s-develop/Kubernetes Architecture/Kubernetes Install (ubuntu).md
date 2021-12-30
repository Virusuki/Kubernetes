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

- docker info로 systemd 확인
```
docker info 
```

- 클러스터 구성
- 마스터노드 1대로 구성하여 진행
```
kubeadm init
```

- kube의 config 셋팅
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

- kubeadm init 설치 완료 후 join 명령어 전체 복사 및 worker 노드에 실행
```
kubeadm join Master-IP:6443 --token udab2y.4rismfx4q5zhbszp \
        --discovery-token-ca-cert-hash sha256:xxxxx~
```

- kubectl get node 명령어를 통해 아래와 같이 클러스터 연결 확인
```
root@namuk-01:~# kubectl get node
NAME       STATUS     ROLES                  AGE     VERSION
namuk-01   NotReady   control-plane,master   5m47s   v1.23.1
namuk-02   NotReady   <none>                 52s     v1.23.1
namuk-03   NotReady   <none>                 50s     v1.23.1
namuk-04   NotReady   <none>                 48s     v1.23.1
```

- 노드들은 NotReady 상태이며, addons를 설치함
- addons 네트워크는 Weave Net으로 설치 진행
- 마스터노드에서만 명령어 실행
```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

- 네트워크 연결 확인
```
root@namuk-01:~# kubectl get node
NAME       STATUS   ROLES                  AGE     VERSION
namuk-01   Ready    control-plane,master   9m13s   v1.23.1
namuk-02   Ready    <none>                 4m18s   v1.23.1
namuk-03   Ready    <none>                 4m16s   v1.23.1
namuk-04   Ready    <none>                 4m14s   v1.23.1
```  


- ## dpkg 관련 에러 일때
``` 
1번째
sudo apt-get install -y ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg]
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker.io
--------------------------------------------

2번째
sudo rm -vf /var/lib/apt/lists/*
sudo apt-get update
sudo touch /var/lib/dpkg/status
sudo apt update && sudo apt upgrade

```  


Reference:
- https://stackoverflow.com/questions/43794169/docker-change-cgroup-driver-to-systemd
- https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
- https://kubernetes.io/docs/concepts/cluster-administration/addons/
- https://www.weave.works/docs/net/latest/kubernetes/kube-addon/


