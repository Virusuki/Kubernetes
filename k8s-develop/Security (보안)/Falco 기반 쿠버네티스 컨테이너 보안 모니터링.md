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
- 룰 조건 
```
- macro: container  
condition: container.id != host  # 컨테이너 ID가 host 이면 안된다. 즉, 컨테이너로 보겠다.

- macro: spawned_process   
condition: evt.type = execve and evt.dir=<   # 이벤트 type이 execve 이여야하고, evt.dir 포함되어야 하며, 프로세스를 실행되는 내용이여야 한다.

- rule: run_shell_in_container
desc: a shell was spawned by a non-shell program in a container. Container entrypoints are excluded.
condition: container and proc.name = bash and spawned_process and proc.pname exists and not proc.pname in (bash, docker) 
#해당 조건 풀이, 컨테이너(==True)와 spawned_process(==True)이고, bash여야하고 proc.pname(parent 프로세스여야하고 고아 프로세스는 안된다). 마지막으로 proc.pname(parent-name)이 bash or 도커는 안된다.(즉, 컨테이너 내에서 특정한 프로세스가 내부적으로 bash를 실행하면 그때 탐지하는 룰이다.)

output: "Shell spawned in a container other than entrypoint (user=%user.name container_id=%container.id
container_name=%container.name shell=%proc.name parent=%proc.pname cmdline=%proc.cmdline)“
priority: WARNING
```

## flaco 설치


