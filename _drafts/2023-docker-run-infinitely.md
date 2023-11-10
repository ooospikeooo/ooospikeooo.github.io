---
layout: post
title: docker run infinitely
date: 2023-03-20 16:30:00 +0900
category: [docker]
tags: [docker, spring]
---

# 목표
 * docker 에서 spring jar 파일을 계속 띄워놓기

# 내용
 * docker run -d -p 8086:8080 --name my-app-daemon my-app tail -f /dev/null