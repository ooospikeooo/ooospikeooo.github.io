---
layout: post
title: 처음 ssh 로그인시 ask 없이하기
date: 2023-12-08 11:40:00 +0900
category: [ssh]
tags: [ssh]
---

# 목표
 * 처음 ssh 로그인시 ask 없이하기
 * 
# 내용
 * ssh_config 의 StrictHostKeyChecking 의 값을 accept-new로 한다.
    ```
    Host *
        StrictHostKeyChecking accept-new
    ```
* ssh_config 파일 위치
  * windows : %HOMEPATH%/.ssh
  ```
    cd %HOMEPATH%/.ssh
    type nul > config
  ```
  * linux : ~/.ssh
  ```
    cd ~/.ssh
    touch config 
  ```