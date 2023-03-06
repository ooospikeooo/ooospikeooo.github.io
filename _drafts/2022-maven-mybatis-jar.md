---
layout: post
title: maven mybatis 공통모듈 jar 파일로 만들기
date: 2022-10-13 16:00:00 +0900
category: [maven]
tags: [maven]
---

# 메모
* mybatis mapperLocations 세팅에서 classpath가 아니라 classpath* 로 하면 jar 까지 살펴본다.
* mybatis typealias 설정 대상이 lib과 app둘로 나누어진다. 
    * context-mybatis.xml의 SqlSessionFactoryBean설정에 typeAliasesPackage를 추가하였으나 의도대로 안됨.
    * 미들웨어는 잘된다. (spring boot이고 어노테이션 세팅이긴함)
    * app에서 typeAliasesPackage의 value가 리스트로 적용도 안되고 classpath*적용도 안됨
    * 미들웨어는 mybatis-spring.jar 파일 버전이 2.0.7 이고 app은 1.2.5
    * app의 mybatis-spring.jar도 버전을 2.0.7로 올림
    * 오류남
    * app의 mybatis.jar 도 버전을 올림
    * 잘됨