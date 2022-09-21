---
layout: post
title: Ansible 사용하여 zookeeper 설치
date: 2022-09-20 9:17:00 +0900
category: [kafka]
tags: [kafka]
---

# 목표
 * virtualbox에서 ansible을 사용하여 zookeper 1대, kafka 1대 설정하여 producer, consumer test

# 전제조건
 * ansible control node
   * ubuntu 설치됨
 * zookeeper node
   * ubuntu 설치됨( birdge 네트워크 )
   * ip : 192.168.62.154
 * ubuntu 설치된 instance. kafka node 용도.
   * ubuntu 설치됨( birdge 네트워크 )
   * ip : 192.168.62.155

# ansible 용 ssh key 생성/복제
 * ssh key 생성
   * ansible control node에서 ssh key를 생성한다. 
     ```
     ssh-keygen
     ```
   * 이미 zookeeper로 복사한 ssh key가 있으면 그것을 사용한다.
 * ssh key 복사
   ```
   sudo scp /home/user/.ssh/id_rsa.pub user@192.168.62.155:/home/user/.ssh
   ```
 * authorized_keys 로 변경
    ```
    rm authorized_keys
    mv id_rsa.pub authorized_keys 
    ```
 * permission 변경
    ```
    sudo chmod 600 authorized_keys
    ```
 * [TODO] 이것도 ansible로 할 수 있음.

# /etc/hosts 파일 수정
 * hosts 파일에 ansible-zk01, ansible-kafka01 정보를 추가한다.
   ```
   ...
   192.168.62.154 ansible-zk01
   192.168.62.155 ansible-kafka01
   ...
   ```
 * ping test로 확인한다.
   ```
   ping -c 2 ansible-zk01
   ping -c 2 ansible-kafka01
   ```

# ansible playbook의 파일 설정
 * '실전 카프카 개발부터 운영까지' 책의 소스로 작업하였다.
    * https://github.com/onlybooks/kafka2/tree/main/chapter2/ansible_playbook
    * 책은 aws였지만, 설치환경은 ubuntu라서 몇개가 에러가 났다.
        * yum -> package
        * java jdk 패키지명 수정
        * kerberos 관련 제거
        * user, 서버 관련 수정
 * ./hosts
    ```
    [zkhosts]
    ansible-zk01

    [kafkahosts]
    ansible-kafka01
    ```
 * ./group_vars/all.yml
    ```
    user_name:
    kadmin_pass:
    kafkaversion: 2.6.0
    ```
 * ./group_vars/kafkahosts.yml
    ```
    brokerid: "{{ inventory_hostname | regex_search('(ansible-kafka0[1-9])') | regex_replace('^ansible-kafka0', '') }}"
    zookeeperinfo: ansible-zk01:2181
    dir_path: /data/kafka-logs
    ```
   * brokerid 의 경우 ansible template에서 ansible-kafka01 -> 1 로 바뀐다.
 * ./group_vars/zkhosts.yml
    ```
    zookeeperversion: zookeeper-3.5.9
    myid: "{{ inventory_hostname | regex_search('(ansible-zk0[0-9])') | regex_replace('^ansible-zk0', '') }}"
    dir_path: /data/zk
    ```
    * myid 의 경우 brokerid 와 마찬가지
 * ./roles/common/tasks/main.yml

    ```
    ---
    - name: Set timezone to Asia/Seoul
      timezone:
        name: Asia/Seoul

    - name: install Java and tools
      package:
        name: ['dstat', 'openjdk-8-jre', 'openjdk-8-jdk', 'git']
        state: latest
    ```
 * ./zookeeper.yml

    ```
    ---
    - hosts: zkhosts
    become: true
    connection: ssh
    roles:
    - common
    - zookeeper
    ```
    * 이렇게 하면 ansible-playbook 실행시 TASK [Gathering Facts] 에서 "Missing sudo password" 에러가 발생한다.
    * bocome: ture 로 privilege 조정시 default로 root 계정으로 되는데, 암호가 없다.
       * -K 옵션 넣어주면 become password를 묻는 prompt가 나타난다.
 