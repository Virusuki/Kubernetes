# 네트워크 정책 (NetworkPolicy)
- 쿠버네티스 아키텍처에는 가능한 애플리케이션 간의 네트워크의 제한을 설정하는게 좋다.
  - 예상치 못한 통신이 일어나지 않게끔 예방한다.
  - 불필요한 통신에 의해 악성행위들이 전파될 수 있는 환경들을 만들 수가 있으므로,
    불필요한 pod들간 통신을 제한한다.
  - 쿠버네티스 안에 방화벽을 만드는 것은 NetworkPolicy를 통해 구성할 수 있다. 

## 이그레스 (Egress)
- 선택된 pod에서 나가는 트래픽에 대한 정책을 설정한다.
- ipBlock을 통해 허용하고자 하는 ip 대역 설정이 가능하다.
- Pots에 어떤 포트를 허용하는지에 대해 명시한다.

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy-ex1
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

