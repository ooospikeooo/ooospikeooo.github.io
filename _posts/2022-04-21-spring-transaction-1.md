---
layout: post
title: Spring Transaction 1 - 기본사항들
# date: 2022-04-21 16:18:00 +0900
category: spring
tags: [spring, transaction]
---

# 목표
* 트랜잭션이란
* Spring Transaction이 하는 일
* 트랜잭션 설정 방법
* 주의점

## 트랜잭션이란
* 한꺼번에 되거나, 한꺼번에 안되거나

## Spring Transaction이 하는 일
* 트랜잭션 자동 시작, 커밋, 롤백
    * 트랜잭션을 직접 코딩하면, connection을 얻고, autoCommit을 끄고, 커밋을 하고 등등을 직접 작성해 줘야한다.
        ``` java
        public void myFunction(){
            Connection con
            try{
                con = DriverManager.getConnection(URL, USER, PW);
                con.setAutoCommit(false);

                // 작업들

                con.commit();
            }catch(Exception e){
                con.rollback();
            }
        }
        ```
        * setAutoCommit(false)를 하지 않으면 sql executeUpdate와 동시에 자동으로 커밋되버린다.
    * Spring Transaction을 이용하면 자동으로 해준다.
        ``` java
        public void myFunction(){
            // 작업들
        }
        ```
        * 사라진 코드의 역할을 Spring이 대신 해준다.
        * Spring이 proxy 패턴을 통해 해당 코드의 역할을 수행한다.
        * proxy 방법은 JDK, CGLib 방법이 있다. spring boot는 CGLib 방법을 사용하고 나머지 경우는 JDK가 default이다.<!--TODO:더살펴볼것-->
            * JDK 방법으로 proxy 설정하려면, 해당코드가 interface와 impl로 나뉘어 구현되어 있어야 한다.<!--TODO:더살펴볼것-->
* Connection 관리
    * 위와 같이 직접 코딩하면 매번 커넥션을 얻어오지만, Spring Transaction을 이용하면 Spring이 알아서 관리해준다.
    * 멀티스레드에서도 안전하다.<!--TODO:더살펴볼것-->
* 트랜잭션 옵션 관리
    * isolation, propagation, readOnly, rollbackFor, timeout 등의 옵션을 관리해준다.

## 트랜잭션 설정 방법
* Spring Transaction 설정은 xml, annotation, programming 을 통해서 할 수 있다.

## 주의점
* 같은 클래스내의 transaction method 호출시 proxy 주입이 안되므로 트랜잭션이 자동으로 동작하지 않는다.(JDK경우)
* private로 설정된 method도 트랜잭션 동작하지 않는다. 에러도 안난다.
