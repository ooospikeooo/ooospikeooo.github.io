---
layout: post
title: Elastic APM
date: 2023-11-13 16:17:00 +0900
category: [elastic]
tags: [elastic]
---

# 개요
* 작업순서
  * elastic search, kibana
  * APM Server
  * APM Java Agent 

* Elastic Search
  * docker network create elastic
  * docker pull docker.elastic.co/elasticsearch/elasticsearch:8.11.0
  * docker run --name es01 --net elastic -e "discovery.type=single-node" -p 9200:9200 -d docker.elastic.co/elasticsearch/elasticsearch:8.11.0
  * docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
    * JBhyeaPaAGAA*JX8j-rP
  * docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
    * eyJ2ZXIiOiI4LjExLjAiLCJhZHIiOlsiMTcyLjE4LjAuMjo5MjAwIl0sImZnciI6IjM2Nzc2MzY5MmIwMzBjZTljYjdiMDBhZTc2MjEwNGZmZDgzODQzNjkyMzU3MDQ1ZDI4Mjk1Nzg4YmRmNDY2YjciLCJrZXkiOiJmMG5ieDRzQklBellHVXNiOWdVajpqYV9kMmRON1NGdWpHX2xlYzk3ZlZ3In0=
* Kibana
  * docker pull docker.elastic.co/kibana/kibana:8.11.0
  * docker run --name kib01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.11.0
* AMP Server
  * docker pull docker.elastic.co/apm/apm-server:8.11.0
  * curl -L -O https://raw.githubusercontent.com/elastic/apm-server/master/apm-server.docker.yml
  * docker run -d -p 8200:8200 --name=apm-server --user=apm-server --volume="%cd%/apm-server.docker.yml:/usr/share/apm-server/apm-server.yml:ro" docker.elastic.co/apm/apm-server:8.11.0 --strict.perms=false -e -E output.elasticsearch.hosts=["https//:localhost:9200"]

* docker-compose
  * 참고 : https://velog.io/@tmdtjq32/%EC%84%B1%EB%8A%A5-%ED%85%8C%EC%8A%A4%ED%8A%B8%EB%A5%BC-%EC%9C%84%ED%95%9C-elastic-APM-%EC%A0%81%EC%9A%A9-2

* APM agent 
  * 
  ```
  java -javaagent:/path/to/elastic-apm-agent-<version>.jar 
    -Delastic.apm.service_name=my-application 
    -Delastic.apm.server_urls=http://localhost:8200 
    -Delastic.apm.secret_token= 
    -Delastic.apm.environment=production 
    -Delastic.apm.application_packages=org.example 
    -jar my-application.jar
  ```