# flaco 개요
- 클라우드 네이티브 런타임 보안 프로젝트인 falco는 쿠버네티스 위협 탐지 엔진
- CNCF에 합류한 최초의 런타임 보안 프로젝트
- 예기치 않은 애플리케이션 동작을 감지하고 런타임 시 위협에 대해 경고

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

- syslog에 falco를 grep 하면 관련 로그 확인이 가능
```
Sep 22 05:44:13 node01 falco[123799]: 05:44:13.306534965: Error Package management process launched
in container (user=root user_loginuid=-1 command=apt update container_id=4831e292a3c8
container_name=k8s_py_py_default_13877cb8-d825-4831-a8ca-f21a9d79b7f0_0 image=python:3.7)
Sep 22 05:44:13 node01 falco: 05:44:13.306534965: Error Package management process launched in
container (user=root user_loginuid=-1 command=apt update container_id=4831e292a3c8
container_name=k8s_py_py_default_13877cb8-d825-4831-a8ca-f21a9d79b7f0_0 image=python:3.7)
Sep 22 05:45:02 node01 falco[123799]: 05:45:02.431342562: Error Package management process launched
in container (user=root user_loginuid=-1 command=apt install -y vim container_id=48f80132919f
container_name=k8s_py_py_default_2293f44d-dfd3-4508-b001-9fc0d43a4c9e_0 image=python:3.7)
Sep 22 05:45:02 node01 falco: 05:45:02.431342562: Error Package management process launched in
container (user=root user_loginuid=-1 command=apt install -y vim container_id=48f80132919f
container_name=k8s_py_py_default_2293f44d-dfd3-4508-b001-9fc0d43a4c9e_0 image=python:3.7)
```


## flaco 설치


