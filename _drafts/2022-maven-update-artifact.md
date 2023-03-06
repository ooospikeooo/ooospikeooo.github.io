---
layout: post
title: maven artifact 업데이트 하기
date: 2022-10-05 16:00:00 +0900
category: [maven]
tags: [maven]
---

# 목표
 * pom.xml 의 dependency 설정된 atrifact들을 업데이트 한다.

# 전제조건
 * maven 설치 + path 설정됨
 * .m2 폴더 아래에 settings.xml 있을 것

# 업데이트 하기
 * cmd 창에서 해당프로젝트의 pom.xml이 있는 곳으로 이동
 * mvn dependency:resolve