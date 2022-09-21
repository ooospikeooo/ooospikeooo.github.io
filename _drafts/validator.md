---
layout: post
title: Validator
date: 2022-05-20 13:17:00 +0900
category: [spring, java]
tags: [spring, java]
---

# 메모
* hibernate version 
* 소스코드 뭔가 꼬임 -> rebuild 하니 해결
* @NotBlank등 어노테이션이 정상적으로 파싱안됨
    * javax.validation 어노테이션을 import해야하는데, org.springmodules.validation.bean.conf.loader.annotation.handler.NotBlank; 을 import 했었음