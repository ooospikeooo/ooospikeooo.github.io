---
layout: post
title: maven executable jar 만들기
date: 2022-10-05 16:00:00 +0900
category: [maven]
tags: [maven]
---

# 목표
 * lib를 갖는 executable jar 파일을 maven을 통해 빌드한다.

# snapshot lib을 timestamp 없이 복사하기.
 * maven-dependency-plugin 에서는 useBaseVersion 을 true 로
 * maven-jar-plugin 에서는 useUniqueVersions 을 false 로