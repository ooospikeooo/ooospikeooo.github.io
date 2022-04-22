---
layout: post
title: Spring Transaction 3 - 테스트(annotation)
date: 2022-04-22 10:00:00 +0900
category: spring
tags: [spring, transaction, log]
---

# 목표
* 로그 설정
* 트랜잭션 테스트

# 준비
* spring transaction test(xml)

# 로그 설정
* 로그가 출력되도록하면 로그를 통해 트랜잭션이 동작하는지를 정확하게 알 수 있다.

## log dependency 추가
* 
    ```xml
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.14</version>
    </dependency>
    ```

## log 설정파일 추가
* src > main > resources 폴더에 log4j.properties 파일을 만들어 준다.

    ![](/public/img/post/2022-04-spring-transaction-3/01.png)

* 파일 내용
    ```properties
    log4j.rootLogger=debug, stdout
    log4j.appender.stdout=org.apache.log4j.ConsoleAppender
    log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
    log4j.appender.stdout.layout.ConversionPattern=[%d{yyyy-MM-dd HH:mm:ss}] [%t] [%-5p] %c - %m%n
    ```

# 트랜잭션 테스트
## 파일들
### context.xml
* applicationContext_annotation.xml을 생성하고 아래 내용을 붙여넣는다.
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans
            xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
            xmlns:tx="http://www.springframework.org/schema/tx" xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
                                    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

        <context:component-scan base-package="com">
            <context:include-filter expression="org.springframework.stereotype.Service" type="annotation" />
        </context:component-scan>

        <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
            <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
            <property name="url" value="jdbc:mysql://localhost:3306/transaction_test?serverTimezone=Asia/Seoul&amp;&amp;useSSL=false" />
            <property name="username" value="tester" />
            <property name="password" value="jin1234" />
        </bean>

        <!--    &lt;!&ndash; @Transactional 어노테이션을 이용하는 경우 &ndash;&gt;-->
        <tx:annotation-driven transaction-manager="txManager"/> <!-- <3> -->

        <bean id="txManager"
            class="org.springframework.jdbc.datasource.DataSourceTransactionManager">  <!-- <1> -->
            <property name="dataSource" ref="dataSource" /> <!-- <2> -->
        </bean>
    </beans>
    ```

### BaseService.java
 * 
    ```java
    public interface BaseService {
        public int insert(Connection con, int id, String name) throws SQLException;
        public void test1() throws SQLException;
        public void test2() throws SQLException;
    }
    ```

### BaseServiceImpl.java
* 
    ```java
    @Service(value="baseService")
    public class BaseServiceImpl implements BaseService {
        @Autowired
        private DataSource dataSource;

        @Transactional(rollbackFor = Exception.class)
        @Override
        public int insert(Connection con, int id, String name) throws SQLException {
            final String SQL= "INSERT INTO TEST_USERS(USER_ID, USER_NM) VALUES (?, ?)"; //sql 쿼리

            PreparedStatement pstml = (PreparedStatement) con.prepareStatement(SQL);
            pstml.setInt(1, id); //1번째 물음표에 remark 삽입
            pstml.setString(2, name); //2번째 물음표에 remark 삽입
            return pstml.executeUpdate(); //쿼리실행 반환 값 삽입한 행의 개수
        }


        //@Transactional
        @Override
        public void test1() throws SQLException{
            final String SQL= "INSERT INTO TEST_USERS(USER_ID, USER_NM) VALUES (?, ?)"; //sql 쿼리

            Connection con = DataSourceUtils.getConnection(dataSource);

            PreparedStatement pstml = (PreparedStatement) con.prepareStatement(SQL);

            pstml.setInt(1, 4); //1번째 물음표에 remark 삽입
            pstml.setString(2, "또치"); //2번째 물음표에 remark 삽입
            System.out.println("before executeUpdate --- 1");
            pstml.executeUpdate(); //쿼리실행 반환 값 삽입한 행의 개수
            System.out.println("after executeUpdate --- 1");


            pstml.setInt(1, 4); //1번째 물음표에 remark 삽입
            pstml.setString(2, "또치"); //2번째 물음표에 remark 삽입
            System.out.println("before executeUpdate --- 2");
            pstml.executeUpdate(); //쿼리실행 반환 값 삽입한 행의 개수
            System.out.println("after executeUpdate --- 2");
        }

        //@Transactional
        @Override
        public void test2() throws SQLException{
            Connection con = DataSourceUtils.getConnection(dataSource);
            int num = insert(con, 4, "또치");
            System.out.println(num + "개 행 삽입 완료");

            num = insert(con, 4, "또치");
            System.out.println(num + "개 행 삽입 완료");
        }
    }
    ```

### TranTest_annotation.java
* 
    ```java
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration("file:src/main/webapp/WEB-INF/applicationContext_annotation.xml")
    public class TranTest_annotation {
        @Autowired
        private DataSource ds;

        @Resource(name="testService")
        TestService testService;

        @Resource(name="baseService")
        BaseService baseService;

        @Test
        public void test() throws Exception{
            try{
                testService.test1();
                System.out.println("testService.test 완료");
            }catch(Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

## 테스트
### 테스트 1 : transaction 없이
* TranTest_annotation.java 에서 test()를 그냥 실행한다.
* 에러가 발생했다.
    ![](/public/img/post/2022-04-spring-transaction-3/02.png)
* 하지만 transaction 설정을 하지 않았으므로 db에 4번 데이터가 하나 들어갔다.
    ![](/public/img/post/2022-04-spring-transaction-3/03.png)

### 테스트 2 : BaseServiceImpl.test1() 에 transaction 설정
* BaseServiceImpl.test1() method 에 @Transactional 어노테이션을 설정한다.
    ```java
    @Transactional // <- 설정해줌
    @Override
    public void test1() throws SQLException{
        // ...
    }
    ```
* test() 실행
* 에러 발생
    ![](/public/img/post/2022-04-spring-transaction-3/04.png)
    * ① 을 보면 transaction이 생성된 것을 알 수 있다.
    * ② -> 에러 발생.
* 하지만 db에 4번 데이터가 하나 들어갔다!

    ![](/public/img/post/2022-04-spring-transaction-3/03.png)
    * 로그를 통해 transaction이 생성된 것을 확인했는데도 rollback이 되지않았다.
    * 오류발생시에 생성된 exception이 SQLException으로 checked exception이어서 transaction rollback처리 되지 않은 것이다. rollback 처리되게 하려면 설정을 해 줘야한다.
        * web module 이용하면 dispatcher에서 error 핸들링할때, 자동으로 checked exception을 unchecked exception으로 바꿔서 던져주는 것 같다. <!--TODO:web 모듈에관해 살펴보기-->
        * test 가 아닌 spring mvc로 테스트 해보면 다른 exception이 들어오고 rollbackFor 설정을 안해줘도 transaction이 잘 동작한다. 

### 테스트 3 : @Transaction 어노테이션에 rollbackFor 옵션추가
* @Transaction 어노테이션에 rollbackFor 옵션을 추가한다.
    ```java
    @Transactional(rollbackFor = Exception.class) // <- rollbackFor 추가
    @Override
    public void test1() throws SQLException{
        //...
    }
    ```
* test() 실행

    ![](/public/img/post/2022-04-spring-transaction-3/05.png)
    * ① -> transaction 생성됨
    * ② -> 이전 테스트와 다르게 rollback 되었다.
    * ③ -> test() method에서 e.printStackTrace();으로 에러가 출력되었다.

    ![](/public/img/post/2022-04-spring-transaction-3/06.png)
    * db에 4번 데이터가 들어가지 않았다.

### 테스트 4: 클래스 내의 함수에 transaction 적용
* test() 함수에서 baseService.test2(); 를 호출한다.
    ```java
    @Test
    public void test() throws Exception{
        try{
            baseService.test2();
            System.out.println("testService.test 완료");
        }catch(Exception e) {
            e.printStackTrace();
        }
    }
    ```
* test() 실행
    ![](/public/img/post/2022-04-spring-transaction-3/07.png)
    * transaction이 생성되지 않았다.

### 테스트 5: @Transaction 어노테이션을 옮겨서
* BaseServiceImpl.java
    ```java
    //@Transactional(rollbackFor = Exception.class) // <- 주석처리
    @Override
    public int insert(Connection con, int id, String name) throws SQLException {
    ```

    ```java
    @Transactional(rollbackFor = Exception.class) // <- @Transaction 설정
    @Override
    public void test2() throws SQLException{
    ```

* 이렇게 하면 
    ```java
    // 여기서
    TranTest_annotation.test()
        ->BaseServiceImpl.test2()
        ->BaseServiceImple.insert() // <- Transaction
    
    // 이렇게
    TranTest_annotation.test()
        ->BaseServiceImpl.test2() // <- Transaction
        ->BaseServiceImple.insert()
    ```
    * transaction이 적용된 함수를 호출하는 시점이 BaseServiceImpl->BaseServiceImpl 의 클래스 내부에서  
    TranTest_annotation->BaseServiceImple 으로 클래스 외부에서 호출하게끔 변경된다.

* test() 실행
    ![](/public/img/post/2022-04-spring-transaction-3/08.png)
    * rollback 되는것 확인.
    * db에도 변경없다.

# 정리
* Spring Transaction을 사용하는 방법이 xml, annotation, programming의 방법이 있다.
* programming으로 하는 방법은 거의 안쓰는듯.
* xml, annotation을 통한 설정 각각의 장단점이 있는 듯하다.
* xml로 설정하면 일괄 처리되어서 반복적으로 설정하지 않아도 되는 반면,  
  annotation으로 하면 필요한 곳에 세세하게 설정이 가능하지 싶다.
* 그 외에 트랜잭션 설정을 하였지만, 위의 경우나, private문제 등등 실제로는 의도와 다르게 작동이 안되는 경우가 많으므로 꼭 확인해 봐야 할 것 같다.

### git
* https://github.com/ooospikeooo/SpringTransactionTest.git
* @Resource 관련 에러가 나는 경우가 있다.
    * 라이브러리가 없어서 난다.
    * File > Project Structure > Project 에서 Project SDK 를 1.8 version으로 바꿔주면 안남.
        * 1.8에는 rt.jar에 관련 라이브러리가 있는데, java 9 부터는 빠졌다고 함
    * maven으로 해당 라이브러리만 추가해주려면
        ```xml
        <dependency>
            <groupId>javax.annotation</groupId>
            <artifactId>javax.annotation-api</artifactId>
            <version>1.3.2</version>
        </dependency>
        ```
        을 추가해주면 됨.

