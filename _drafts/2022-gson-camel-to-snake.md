---
layout: post
title: gson 에서 camel 표기법 을 snake 표기법으로 출력
date: 2022-12-06 10:00:00 +0900
category: [java, json]
tags: [java, json]
---

# 목표
 * gson에서 object 를 snake case로 출력한다.

# 방법
    
    Gson gson = new GsonBuilder()
            .setFieldNamingPolicy(FieldNamingPolicy.LOWER_CASE_WITH_UNDERSCORES)
            .setPrettyPrinting()
            .create();
    String json = gson.toJson(lifeWheatherIndex);
    