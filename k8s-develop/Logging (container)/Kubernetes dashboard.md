# kubernetes dashboard
- kubernetes 클러스터 범용 웹 UI
   - 사용자는 kubernetes dashboard를 통해 응용 프로그램 관리, 문제/해결, 클러스터 관리할 수 있음

## kubernetes Install
- 설치 command

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.4.0/aio/deploy/recommended.yaml


root@ubuntu-kube-master1:~# kubectl get svc -n kubernetes-dashboard
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.104.155.42    <none>        8000/TCP   15s
kubernetes-dashboard        ClusterIP   10.105.119.221   <none>        443/TCP    15s


Reference:
- https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/
- https://github.com/kubernetes/dashboard
