---
layout: post
title: spring으로 mail보내기
date: 2022-12-24 12:00:00 +0900
category: [spring]
tags: [spring]
---

# 목표
 * spring으로 mail보내기

# 방법
## 구글 설정
* 모들 메일에 POP를 활성화 하기
* IMAP 사용

## maven

````
    <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-mail -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-mail</artifactId>
        <version>2.5.6</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/javax.mail/mail -->
    <dependency>
        <groupId>javax.mail</groupId>
        <artifactId>mail</artifactId>
        <version>1.4.7</version>
    </dependency>
````

## spring 
* JavaMailSender Bean
  * SpringBoot 는 자동으로 생성해준다고 함
  * properties
    ````
    spring.mail.host=smtp.gmail.com
    spring.mail.port=587
    spring.mail.username={계정id}
    spring.mail.password={구글2단계앱암호}
    spring.mail.properties.mail.smtp.auth=true
    spring.mail.properties.mail.smtp.starttls.enable=true
    ````

* test 코드
    ````
    @SpringBootTest
    @ActiveProfiles("dev")
    class WsMwApplicationTests {

        @Autowired
        JavaMailSender emailSender;

        @Test
        void contextLoads() {
        }

        @Test
        void sendEmail(){
            SimpleMailMessage message = new SimpleMailMessage();
            message.setFrom("test@gmail.com");
            message.setTo("test@hanmail.net");
            message.setSubject("spring main test");
            message.setText("mail send success.");
            emailSender.send(message);
        }
    }
    ````
