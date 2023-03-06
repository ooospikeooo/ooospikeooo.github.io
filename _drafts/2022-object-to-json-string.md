---
layout: post
title: object를 json형식의 string으로 변환
date: 2022-12-15 16:00:00 +0900
category: [java, json]
tags: [java, json]
---

# 목표
 * Java object를 json형식의 string으로 변환한다.

# 방법
 * 
    ````
    Gson gson = new GsonBuilder()
                .setDateFormat("yyyy-MM-dd HH:mm:ss")
                .setFieldNamingPolicy(FieldNamingPolicy.LOWER_CASE_WITH_UNDERSCORES)
                .setPrettyPrinting()
                .create();

    String json = gson.toJson(requestPacket);
    ````