---
layout: post
title: IntelliJ에서 Spring MVC Project 만들기
date: 2022-04-20 10:38:00 +0900
category: spring
tags: [spring, mvc, intellij]
---

# 목표
* intellij에서 spring mvc 프로젝트를 생성하고 jsp페이지가 정상적으로 뜨는지 테스트한다.

## 프로젝트 생성
1. FIle > New >  Project 선택
1. New Project 창에서 설정

    ![title](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_01.png)

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_02.png){: width="500"}

1. Add Frameworks Support > Spring MVC 선택

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_03.png)

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_04.png){: width="550"}



## 자동으로 생성된 것들 정리
1. Maven을 이용해서 라이브러리를 관리할 것이므로, 자동으로 생성된 lib 폴더를 삭제해 준다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_05.png)

1. File > Project Structure > Libraries 에서 없는 라이브러리를 삭제 한다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_08.png)


## 폴더구조 변경
1. web 폴더를 src/main/ 아래로 옮기고, 폴더 이름을 webapp으로 변경한다. 

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_09.png)

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_10.png)

    * 안해줘도 되지만 pom파일에 `<packaging>war</packaging>`를 추가했을 때, 이후에 pom파일을 수정하면 intelliJ가 자동으로 프로젝트 module 설정을 변경해버려서 매번 수정해 줘야하는 번거로움이 있다.

1. File > Project Structure > Facets 에서 Web Resource Directory 폴더를 제대로 설정해준다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_11.png)

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_12.png)

## Maven 라이브러리 설정
1. pom.xml 파일에서 spring-webmvc를 추가한다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_06.png){: width="600"}

    ```xml
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <org.springframework-version>4.1.2.RELEASE</org.springframework-version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>
    </dependencies>
    ```

1. IntelliJ 오른쪽에 'Load Maven Change'버튼을 눌러 라이브러리를 로드한다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_07.png)

1. File > Project Structure > Artifacts 에서 Available Elements 부분에서 프로젝트 폴더를 선택하고 오른쪽 마우스를 클릭 후 'Put into Output Root'를 선택한다. 
Maven에서 다운받은 라이브러리들이 WEB-INF > lib 폴더로 들어간다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_13.png)

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_14.png)

    * 수동으로 하지 않고 IntelliJ가 자동으로 해주는 경우가 있던데 어떻게 설정하는 지 모르겠다. 그렇게 설정되면 'Output Layout' 옆에 'Maven'이라는 항목이 생성된다.

    * ->pom.xml에 `<packaging>war</packaging>` 추가하면됨.
    
    * 안하면 git에 올리고 다시 받을때, 이것 말고도 web모듈이 빠져버려서 다시 수동으로 설정해줘야한다.

## web 설정, spring 설정
1. web.xml 파일의 내용을 수정한다.
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/applicationContext.xml</param-value>
        </context-param>
        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
        <servlet>
            <servlet-name>dispatcher</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>/WEB-INF/dispatcher-servlet.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatcher</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>
    </web-app>
    ```

1. dispatcher-servlet.xml 파일을 수정한다.
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

        <mvc:annotation-driven />

        <context:component-scan base-package="com">
        </context:component-scan>

        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/views/" />
            <property name="suffix" value=".jsp" />
        </bean>
    </beans>
    ```

## 소스코드 생성
1. java 폴더에 com package를 생성 후 HomeController.java 파일을 만든다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_15.png)

    ```java
    package com;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;

    @Controller
    public class HomeController {

        @RequestMapping("/")
        public String home() {
            return "test";
        }
    }
    ```

1. WEB-INF 폴더에 views 폴더를 만들고 test.jsp 파일을 만든다. webapp > WEB-INF > index.jsp 파일은 지워준다.

    ![](/public/img/post/2022-04-20-SpringMVC-project-new/spring_mvc_project_new_16.png)

    ```javascript
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>$Title$</title>
    </head>
    <body>
        $END$
    </body>
    </html>
    ```


## 실행환경 설정
1. Add Configuration 항목에서 Tomcat Server 를 Local로 add하고 Deployment 탭에서 artifacts를 등록, Application context 란에 /    <-- 이것만 남긴다.  
    자세한 내용은 검색하면 많이 나옴.

1. 간혹 여기까지 해도 에러나는 경우가 있다. 이유는 모르겠으나, iml파일에 
    ```xml
    <component name="NewModuleRootManager" inherit-compiler-output="true">
        <exclude-output />
        <content url="file://$MODULE_DIR$" />
        <orderEntry type="inheritedJdk" />
        <orderEntry type="sourceFolder" forTests="false" />
    </component>
    ```
    코드가 포함되어 있는 경우가 있는데, 지워주면 잘 된다 ;;;;

### git 
    https://github.com/ooospikeooo/SpringMVCTest.git