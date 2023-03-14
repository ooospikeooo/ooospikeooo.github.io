---
layout: post
title: spring requestbody로 json데이터 받기
date: 2023-03-14 11:00:00 +0900
category: [spring]
tags: [spring]
---

# 목표
 * spring controller에서 requestbody로 json 데이터 받는 것을 살펴본다.

# 내용
 * 아래 json 전달하기
    ```
    {
       "name":"aaa",
       "id":"bbb"
    }
    ```
 * String 으로 받기
    * 그냥 통짜로 받음
    * {\r\n   "name":"aaa",\r\n   "id":"bbb"\r\n}
 * Map<String,String> 으로 받기
    * key,value pair로 자동 변환 됨
    * ("name", "aaa"), ("id", "bbb")