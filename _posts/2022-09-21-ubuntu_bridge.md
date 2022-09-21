---
layout: post
title: Bridge 네트워크 설정으로 Ubuntu 설치
date: 2022-09-20 9:17:00 +0900
category: [ubuntu]
tags: [ubuntu]
---

# 목표
 * virtual box에서 Bridge 네트워크로 ubuntu를 설치한다.
 * 설치한 ubuntu에서 외부로 네트워크 되는지 확인.

# Virtual Box
## ubuntu 다운로드
   * https://www.virtualbox.org/wiki/Downloads
## 네트워크 설정(bridge)
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/setting.png)
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/network_bridge.png)
## ubuntu 설치
* 다운받은 ubuntu iso를 선택한다.
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ide.png)
* 상단 메뉴의 시작 선택
* ubuntu 설치화면이 뜬다
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_01.png)
* 설치 선택
* DHCP로 자동으로 할당받은 ip를 수동을 설정한다.(필요없으면 패스.)
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_02.png)
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_03.png)
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_04.png)
   * Subnet, Address, Gateway, Name server 정보를 작성한다.
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_05.png)
   
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_06.png)
  * 설정이 완료되고 공유기에도 등록이 되었다.
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/iptimes.png)
* 나머지는 그냥 설치해주면된다. openSSH는 포함하였다
* 설치 완료. 인터넷 테스트
   ![](/public/img/post/2022/kafka_1zk1kafka_ansible/ubuntu_internet_ok.png)
