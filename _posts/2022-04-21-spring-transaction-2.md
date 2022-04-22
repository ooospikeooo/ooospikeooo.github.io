---
layout: post
title: Spring Transaction 2 - 테스트(xml)
date: 2022-04-21 16:18:00 +0900
category: spring
tags: [spring, transaction]
---

# 목표
* 트랜잭션 사용하지 않고 오류상황 테스트
* 트랜잭션 사용하고 오류상황 테스트

# 준비
* spring mvc project
* spring test
* spring mysql 연결


## 공통사항
### 오류상황
* db table을 (USER_ID, USER_NM)으로 만들었다.

    ![](/public/img/post/2022-04-spring-transaction-2/01.png)
* 여기에 (4, 또치)를 두번 넣어서 오류상황을 만들 것이다.
* 파일들
    * BaseService.java
        ```java
        public interface BaseService {
            public int insert(Connection con, int id, String name) throws SQLException;
            public void test2() throws SQLException;
        }
        ```
    * BaseServiceImpl.java
        ```java
        @Service(value="baseService")
        public class BaseServiceImpl implements BaseService {
            @Autowired
            private DataSource dataSource;

            @Override
            public int insert(Connection con, int id, String name) throws SQLException {
                final String SQL= "INSERT INTO TEST_USERS(USER_ID, USER_NM) VALUES (?, ?)"; //sql 쿼리

                PreparedStatement pstml = (PreparedStatement) con.prepareStatement(SQL);
                pstml.setInt(1, id); //1번째 물음표에 remark 삽입
                pstml.setString(2, name); //2번째 물음표에 remark 삽입
                return pstml.executeUpdate(); //쿼리실행 반환 값 삽입한 행의 개수
            }

            @Override
            public void test2() throws SQLException{
                try {
                    Connection con = DataSourceUtils.getConnection(dataSource);
                    int num = insert(con, 4, "또치");
                    System.out.println(num + "개 행 삽입 완료");

                    num = insert(con, 4, "또치");
                    System.out.println(num + "개 행 삽입 완료");

                    System.out.println("before RuntimeException");
                }catch (SQLException e){
                    e.printStackTrace();
                    System.out.println("SQLException");

                    throw new SQLException();
                }
        //        throw new RuntimeException();
            }
        }
        ```

### maven dependency 추가
* 
    <!--TODO:각 라이브러리 역할 살펴보기-->
    ```xml
    <dependency>
        <groupId>commons-dbcp</groupId>
        <artifactId>commons-dbcp</artifactId>
        <version>${commons-dbcp.version}</version>
    </dependency>

    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.0</version>
    </dependency>
    ```

### context.xml 설정
* applicationContext_xml.xml파일을 생성한다.
* context.xml을 test용 java파일에서 각각 읽기때문에, Service annotation이 스캔되게끔 설정해야한다.
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans
            xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
            xmlns:tx="http://www.springframework.org/schema/tx" xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                                    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

        <!-- Service 어노테이션을 사용하므로 설정해준다. -->
        <context:component-scan base-package="com">
            <context:include-filter expression="org.springframework.stereotype.Service" type="annotation" />
        </context:component-scan>

        <!-- db 설정 -->
        <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
            <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
            <property name="url" value="jdbc:mysql://localhost:3306/transaction_test?serverTimezone=Asia/Seoul&amp;&amp;useSSL=false" />
            <property name="username" value="----" />
            <property name="password" value="----" />
        </bean>
    </beans>
    ```

## 트랜잭션 사용하지 않고 오류상황 테스트
* 파일들
    * TranTest_xml.java
        ```java
        @RunWith(SpringJUnit4ClassRunner.class)
        @ContextConfiguration("file:src/main/webapp/WEB-INF/applicationContext_xml.xml")
        public class TranTest_xml {
            @Autowired
            private DataSource ds;

            @Resource(name="baseService")
            BaseService baseService;

            @Test
            public void test() throws Exception{
                try{
                    baseService.test2();
                    System.out.println("baseService.test 완료");
                }catch(Exception e) {
                    e.printStackTrace();
                }
            }
        }
        ```
* 결과
    * TranTest_xml.java의 test()함수를 실행한다.
    * log

        ![](/public/img/post/2022-04-spring-transaction-2/02.png)
    * db

        ![](/public/img/post/2022-04-spring-transaction-2/03.png)
        * 4번행에 데이터가 들어가 있다.


## 트랜잭션 사용하고 오류상황 테스트
* context.xml파일 수정
    * 아래내용을 추가해 준다.
    ```xml
        <bean id="txManager"
            class="org.springframework.jdbc.datasource.DataSourceTransactionManager">  <!-- <1> -->
            <property name="dataSource" ref="dataSource" /> <!-- <2> -->
        </bean>

        <tx:advice id="txAdvice" transaction-manager="txManager">
            <tx:attributes>
                <tx:method name="*" propagation="REQUIRED" rollback-for="Exception"/>
            </tx:attributes>
        </tx:advice>

        <aop:config>
            <aop:pointcut id="txPointcut" expression="execution(* com..*Impl.*(..))" />
            <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut" />
        </aop:config>
    ```

* 결과
    * TranTest_xml.java의 test()함수를 실행한다.
    * log

        ![](/public/img/post/2022-04-spring-transaction-2/04.png)
        * 마찬가지로 에러 발생.
    * db

        ![](/public/img/post/2022-04-spring-transaction-2/05.png)
        * 4번행에 데이터가 없다.