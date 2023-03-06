---
layout: post
title: intellij 에서 maven snapshot 버전 갱신안되는 문제
date: 2022-10-27 16:00:00 +0900
category: [intellij, maven]
tags: [intellij, maven]
---

# 목표
 * intellij 에서 maven snapshot 버전 갱신안되는 문제 해결한다.

# 현상
 * dependency 에 snapshot 이 있을 경우
 * maven의 경우 build에서 아래의 useUniqueVersions를 false로 세팅하면 됨.
 ````
 <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <useUniqueVersions>false</useUniqueVersions>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
 ````
 * intellij 에서는 timestamp 가 찍힌 이전 버전을 build에 사용함

# 해결책
 * File -> Settings 에서 Maven 세팅중 'Always update snapshots'를 enable 해줌