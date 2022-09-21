---
layout: post
title: Argument Resolve
date: 2022-05-19 9:17:00 +0900
category: [spring]
tags: [spring, java, javascript]
---

# 상황
* @RequestBody 추가했더니, Content type 'application/x-www-form-urlencoded;charset=UTF-8' not supported 발생

# 내용
````
resolver ( HandlerMethodArgumentResolver ) 

RequestBody - RequestResponseBodyMethodProcessor
HttpServletRequest - ServletRequestMethodArgementResolver
아무것도없이 - ServletModelAttributeMethodProcessor
````
