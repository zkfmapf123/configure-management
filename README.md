# Configure-Management use Ansible

## 특징

- python 기반
- agentless (각 서버에 agent 설치가필요없다)
  - ssh
  - win_rm

## UseCase

- Configuration Management (형상관리) \*\*\*
- Security Compliance (보안) \*\*\*
  - Security Test
- Application Deployment (Application 배포)
- Provisioning (자원의 provisioning)
- Continuouse Delivery

## why Ansible?

- 기존의 Shell로 형상관리를 진행할때

  ```bash
  which=`which mongod 2>&1 >/dev/null`
      if [&? -eq 0]; then
          if ["%INSTALLED_MONGO" == "$MONGO_VERSION" ]; then
          echo "Mongo Server version is current and up to date"
      fi
      if ["$INSALLED_MONGO" != "$MONGO_VERSION"]; then
        ...
      fi
  ```

- Ansible로 멱등성을 유지하면서 개발할때
  ```hcl
    - name: install mongodb
      yum: name=mongodb-server-2.6 state=installed
  ```

## Ansible

> Inventory

- 형상관리를 하기위한 host(서버)들의 파일 -> 서버들의 목록
- 그룹기능을 지원 (Ubuntu Group, Amazon Linux Group, Dev Group, Prod Group)
- Static Inventory \*\*\*
- Dynamic Inventory -> Instance IP가 빈번하게 바뀔경우에 사용

```inv
    ## Group
    [ubuntu]
    ubuntu1 ansible_host=52.79.243.88 ansible_user=ubuntu
    ubuntu2 ansible_host=54.180.100.70 ansible_user=ubuntu

    ## Sub Group
    [ubuntu:dev]

    [ubuntu:dev]
    dev:ubuntu ansible_host=52.79.243.88 ansible_user=ubuntu

    [ubuntu:prod]
    prod:ubuntu ansible_host=54.180.100.70 ansible_user=ubuntu
```

> playbook

- yaml 파일로 정의 (하나의 Playbook -> Play의 집합)
- play 즉 작업목록, 특정 호스트의 대해 작업을 수행

```
    // 1개의 Playbook
    // play가 2개
    // 각 play안에 Task들이 존재
    // 앤서블이 실행하는 코드 단위 -> module
    //  - 첫번째 play에...
    //  - 첫번째 task는 command 모듈을 사용
    //  - 두번째 task는 script 모듈을 사용
    //  - 세번째 task는 apt 모듈을 사용

- name: Play 1
  hosts: ubuntu
  tasks:
  - name: "Task 1: Execute command"
    command: uptime

  - name: "Task 2: Execute script"
    script: task2.sh

  - name: "Task 3: Install package"
    apt:
      name: nginx
      state: present
      update_cache: true

  - name: "Task 4: Start nginx service"
    service:
      name: nginx
      state: started

- name: Play 2
  hosts: localhost
  tasks:
  - name: "Task 1: Execute command"
    command: whoami

  - name: "Task 2: Execute script"
    script: task2.sh
```

## Reference

- <a href="https://docs.ansible.com/ansible/latest/index.html"> Ansible Document </a>
