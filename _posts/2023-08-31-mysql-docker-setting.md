---
layout: post
title: mysql docker-compose 설정
date: 2023-08-31 10:00:00 +0900
category: [mysql, docker]
tags: [mysql, docker]
---

# 목표
 * 로컬에서 동작하던 mysql test db 를 docker 에 올리기

# 이슈
 * table을 찾을 수 없다고 에러남
   * 원인 
     * mysql의 table name 대소문자구분 옵션이 달라서 발생했음
   * 해결
     * lower_case_table_names=1 으로 세팅하면됨
     * docker-compose.yml에서
       ```
           command:
              - --lower_case_table_names=1
       ```
       추가
 * this is incompatible with sql_mode=only_full_group_by 에러남
   * 원인
     * mysql의 sql_mode모드에 ONLY_FULL_GROUP_BY 옵션때문
   * 해결
     * 마찬가지로 docker-compose.yml 에 command 추가
     ```
         command:
            - --lower_case_table_names=1
            - --sql_mode=STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION
     ``` 