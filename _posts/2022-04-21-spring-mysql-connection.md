---
layout: post
title: Spring Mysql연결하기
# date: 2022-04-20 10:38:00 +0900
category: Spring
---

# 준비사항
* mysql local 서버
* spring test

# 목표
* spring test에서 mysql db에 연결한다.
* mysql에 테이블을 생성한다.
* spring에서 mysql db에 insert 테스트해본다.

### spring test에서 mysql db에 연결한다.
1. pom.xml에 dependency를 추가한다.
    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>${org.springframework-version}</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.11</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>${org.springframework-version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    ```

1. test > java 폴더에 테스트용 java 파일을 생성한다. MySQLTest.java로 생성하였다.

    ![title](/public/img/post/2022-04-spring-mysql-connection/01.png)

1. 연결 테스트코드를 작성한다.
    ```java
    import org.junit.Test;

    import java.sql.Connection;
    import java.sql.DriverManager;
    import java.sql.SQLException;

    public class MySQLTest {
        private static final String DRIVER = "com.mysql.cj.jdbc.Driver";
        private static final String URL = "jdbc:mysql://localhost:3306/transaction_test?serverTimezone=Asia/Seoul&&useSSL=false";
        private static final String USER = "----";
        private static final String PW = "----";

        static {
            try {
                Class.forName(DRIVER);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }//static

        @Test
        public void testConnection(){
            Connection con = null;
            try{
                con = DriverManager.getConnection(URL, USER, PW);
            }catch(SQLException e) {

                e.printStackTrace();
            }
        }//testConnection_
    }
    ```

1. test를 실행한다.

    ![title](/public/img/post/2022-04-spring-mysql-connection/02.png)

1. 결과 확인.

    ![title](/public/img/post/2022-04-spring-mysql-connection/03.png)

### git
    https://github.com/ooospikeooo/SpringMySQLTest.git