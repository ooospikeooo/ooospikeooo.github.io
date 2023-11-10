---
layout: post
title: spring configuration-properties
date: 2023-04-11 10:00:00 +0900
category: [spring]
tags: [spring]
---

# 목표
 * spring configuration-properties 에 대해 알아본다.

# 내용
 * property 파일
    ``` 
    spring:
        config:
            activate:
      on-profile: dev-db-mysql8-mybatis
    datasource:
        jdbc-url: jdbc:mysql://${jdbc-url-base}/openapi_test
        driver-class-name: com.mysql.cj.jdbc.Driver
        username: user
        password: passwd
    ```

 * property 클래스
    ``` java
    @Getter
    @ConfigurationProperties(prefix = "spring.datasource")
    public class DatasourceJdbcUrl {
        @NotEmpty
        private final String jdbcUrl;

        @NotEmpty
        private final String driverClassName;

        @NotEmpty
        private final String username;

        @NotEmpty
        private final String password;

        public DatasourceJdbcUrl(String jdbcUrl, String driverClassName, String username, String password){
            String jdbcUrlBase = System.getProperty("jdbc-url-base");
            Map<String, String> parameters = new HashMap<>();
            parameters.put("jdbcUrlBase", jdbcUrlBase);
            StringSubstitutor substitutor = new StringSubstitutor(parameters);
            String jdbcUrlSubstituted = substitutor.replace(jdbcUrl);

            this.jdbcUrl = jdbcUrlSubstituted;
            this.driverClassName = driverClassName;
            this.username = username;
            this.password = password;
        }
    }
    ```

 * property scan 설정
    ```java
    @ComponentScan(basePackages = {"net.jw"})
    @SpringBootApplication
    @MapperScan(
            basePackages = {"net.jw.**.mapper"},
            annotationClass = Mapper.class
    )
    @ConfigurationPropertiesScan(basePackages = {"net.jw.datacollector.config.**"})
    public class Applications {
        public static void main(String[] args) {
            System.out.println("spring.profiles.active : " + System.getProperty("spring.profiles.active"));
            System.out.println("rest.key : " + System.getProperty("rest.key"));
            SpringApplication.run(Applications.class, args);
        }
    }
    ```