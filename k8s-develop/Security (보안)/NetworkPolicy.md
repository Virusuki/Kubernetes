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
  name: network-policy-egress-ex1
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Egress    # Egress를 설정한다는 의미 (Default deny가 됨), 모든 트래픽에 대해서 모두 거절하게 됨
  egress:     # 다만, 허용함(화이트리스트를 정해줌)
  - to:       # 트래픽을 허용해주는 정책
    - ipBlock:
        cidr: 10.0.0.0/24 # 해당 트래픽은 허용
    ports:
    - protocol: TCP
      port: 5978   
```


## 인그레스 (Ingress)
- 선택된 pod로 들어오는 트래픽에 대한 정책을 설정한다.
- ipBlock을 통해 허용하고자 하는 ip 대역 설정이 가능하다.
- Except를 사용하여 예외 항목 설정이 가능하다.
- namespaceSelector와 podSelector를 사용
  - 그룹별 더욱 상세한 정책 설정이 가능하다.

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy-ingress-ex1
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
      matchLabels:
        project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
```














