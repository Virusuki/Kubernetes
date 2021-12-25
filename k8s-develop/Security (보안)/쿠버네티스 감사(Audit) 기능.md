# 쿠버네티스 감사 기능
- 클러스터에서 발생하는 다양한 작업들에 대한 관리자가 모니터링을 하기 위한 audit 기능을 활성화
- 쿠버네티스 audit 기능을 사용해 실시간으로 사용자들이 사용하는 API 정보를 확인이 가능
- 쿠버네티스 audit는 보안 관련 레코드 세트를 시간순으로 저장 가능
- 클러스터 사용자 정보와 kubernetes API를 사용하는 애플리케이션 및 control plane 자체에서 발생하는 활동을 감사

## 감사 기능을 통해 클러스터 관리자가 확인할 수 있는 정보들 리스트
- What happened?                 (무슨 일이 일어났는가?)
- when did it happen??           (언제 일어났는가?)
- who started it?                (누가 시작했습니까?)
- What has happened in the past? (과거에 무슨일이 일이 있었는가?)
- Where was it observed?         (어디서 관찰되었는가?)
- Where did it start?            (어디에서 시작되었는가?)
- Where do the results go?       (결과는 어디로 갔는가?)

## Kube-apiserver를 통해서 실행 및 기록이 시작
- 감사 레코드는 kube-apiserver를 통해 실행
- 실행의 각 단계에 대한 각 요청은 감사 이벤트를 생성
- 특정 정책에 따라 사전 처리되고 백엔드에 기록
- 각 요청은 연결된 단계에 따라 기록하는 정보가 다름
  - 요청에 따른 기록 정보 (어느 시점에 로그를 남길 것인지)
    - RequestReceived : 감사 핸들러가 요청을 받는 즉시 그리고 핸들러 체인 아래로 위임되기 전에 생성된 이벤트에 대한 단계
    - ResponseStarted : 응답 헤더 전송되었으나 응답 본문이 전송되기 전 상태다. 이 단계는 장기 실행 요청(예: watch)에 대해 생성
    - ResponseComplete : 응답 본문이 완료되었으며 더이상 바이트가 전송되지 않는 단계
    - Panic : 패닉이 발생 시, 생성되는 이벤트

  - 정의된 감사 수준 (어떤 로그에 데이터를 저장할지)
    - None : 이 규칙과 일치하는 이벤트를 기록하지 않는다.
    - Metadata : 요청 메타데이터(요청 사용자, 타임스탬프, 리소스, 동사 등)를 기록하지만 요청 or 응답은 기록 하지않는다.
    - Request : 이벤트 메타데이터 및 요청 본문을 기록하지만 응답 본문은 기록하지 않는다. (리소스가 아닌 요청에는 적용되지 않는다.)
    - RequestResponse : 이벤트 메타데이터, 요청 및 응답 본문을 기록한다. (리소스가 아닌 요청에는 적용되지 않는다.)
  

- vi /etc/kubernetes/audit-policy.yaml 

```
apiVersion: audit.k8s.io/v1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    resources:
    - group: ""
      # Resource "pods" doesn't match requests to any subresource of pods,
      # which is consistent with the RBAC policy.
      resources: ["pods"]
  # Log "pods/log", "pods/status" at Metadata level
  - level: Metadata
    resources:
    - group: ""
      resources: ["pods/log", "pods/status"]

  # Don't log requests to a configmap called "controller-leader"
  - level: None
    resources:
    - group: ""
      resources: ["configmaps"]
      resourceNames: ["controller-leader"]

  # Don't log watch requests by the "system:kube-proxy" on endpoints or services
  - level: None
    users: ["system:kube-proxy"]
    verbs: ["watch"]
    resources:
    - group: "" # core API group
      resources: ["endpoints", "services"]

  # Don't log authenticated requests to certain non-resource URL paths.
  - level: None
    userGroups: ["system:authenticated"]
    nonResourceURLs:
    - "/api*" # Wildcard matching.
    - "/version"

  # Log the request body of configmap changes in kube-system.
  - level: Request
    resources:
    - group: "" # core API group
      resources: ["configmaps"]
    # This rule only applies to resources in the "kube-system" namespace.
    # The empty string "" can be used to select non-namespaced resources.
    namespaces: ["kube-system"]

  # Log configmap and secret changes in all other namespaces at the Metadata level.
  - level: Metadata
    resources:
    - group: "" # core API group
      resources: ["secrets", "configmaps"]

  # Log all other resources in core and extensions at the Request level.
  - level: Request
    resources:
    - group: "" # core API group
    - group: "extensions" # Version of group should NOT be included.

  # A catch-all rule to log all other requests at the Metadata level.
  - level: Metadata
    # Long-running requests like watches that fall under this rule will not
    # generate an audit event in RequestReceived.
    omitStages:
      - "RequestReceived"
```

- /etc/kubernetes/manifests/kube-apiserver.yaml에서 .spec.containers[0].command 에 다음과 같이 인자를 추가
```
# vi /etc/kubernetes/manifests/kube-apiserver.yaml
- --token-auth-file=/etc/kubernetes/pki/somfile.csv #아래에 추가한다.
- --audit-policy-file=/etc/kubernetes/audit-policy.yaml
- --audit-log-path=/var/log/audit.log
```

- 이어서, .spec.container[0].volumeMounts와 .spec.volumes에 다음 내용을 추가
```
volumeMounts:
  - mountPath: /etc/kubernetes/audit-policy.yaml
    name: audit
    readOnly: true
  - mountPath: /var/log/audit.log
    name: audit-log
    readOnly: false
  
  ...#아래의 volumes 라인에도 추가한다.

volumes:
- name: audit
  hostPath:
    path: /etc/kubernetes/audit-policy.yaml
    type: File
- name: audit-log
  hostPath:
    path: /var/log/audit.log
    type: FileOrCreate

```
- 수정한 static-pod가 정상적으로 작동하는지 확인한다.
```
# kubectl get pod -n kube-system
```

- Master 노드에서 볼륨마운트한 저장경로의 로그 확인
```
# tail -f /var/log/audit.log
```




