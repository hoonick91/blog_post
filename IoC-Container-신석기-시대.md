---
title: IoC Container 신석기시대
date: 2019-03-16 12:42:01
tags:
categories: spring framework
---



## IoC Container란?

우선 Container는 무언가를 주체적으로 관리한다. 예를 들어 Servlet Container는 Servlet의 생성 소멸 등을 관리한다. 그리고 IoC란 제어의 역전 즉, 기존에 Client가 객체를 생성하고 소멸시킨(소멸은 GC가 한다) 일들을 Container가 한다는 것이다. 그럼 어떤것이 할까? Application Context가 한다. Application Context에 bean을 등록시켜서 Container가 관리할 수 있게 해준다. 이 ApplciaitonContext가 생성, 소멸할때 발생하는 이벤트를 받는 것이 ContextLoaderListener이고 이때 ContextLoaderListener이 ContextLoader를 실행시켜서 필요한 이벤트를 발생시킨다. 이 ContextLoader에 각종 param을 넣어줄 수 가 있다. 

IoC Container는 2가지가 있다.

1. BeanFactory
2. ApplicationContext

BeanFactory가 Bean을 만든다.(@Component annotaion이 붙은 것이라든지…등) 여러번 호출되는 요청을 요청마다 인스턴스를 만들 면 손해이므로 한번만 만들고 만들어진 인스턴스를 재사용한다. 이것이 Bean으로 만드는 것의 이점이다. 이처럼 자동으로 인스턴스를 만들고 사용이 끝난 인스턴스는 알아서 없애준다. 이는 IoC Container의 역할이다. 

<br>



### Context-Loader-Listener

![image](https://docs.spring.io/spring/docs/current/spring-framework-reference/images/mvc-context-hierarchy.png)



ApplicationContext 생성될때, ContextLoaderListener가 호출된다. (여기서 Root ApplicationContext는 다른 Servlet에서 공유할 수 있다.) 그러면 dispatcher-servlet는 Root webApplicationContext를 상속해서, dispatcher-servlet안에서만 쓸 수 있는 webApplicationContext를 만든다. 

즉 ContextLoaderListner은 두개의 dispatcherServlet에서 공유가능한 Root ApplicationContext가 생성될 때 이벤트를 발생시킨다.

> Root webApplicationContext는 ApplicationContext를 상속받는다.

Spring의 root webApplicationContext가 start up, shut down될떄 사용하는 bootstrap listener?(bootstrap - 외부의 입력없이 스스로 실행되는 것)



### Context-Loader

ContextLoader는 WebApplicationContext를 생성하는 class이다. 이를 상속해서 WebApplicationContext가 생성될때 호출되는 함수가 ContextLoaderListener에 속해있다. 

WebApplicationContext는 생성되면서 여러가지 설정값을 을 넣어줄 수 있는데, 그때 **contextClass**를 param으로 넣을 수 있다. 이때 들어가는 contextClass가 **AnnotationConfigWebApplicationContext은**이다. 이는 WebApplicationContext의 구현체로 annotation이 붙은 클래스를(```@Configuration```) input으로 사용하게 해준다.

ContextLoaderListener이 AnnotationConfigWebApplicationContext 설정값을 가지고, RootWebApplicationContext를 만들고, DispatcherServlet은 servlet-context.xml을 가지고 각각의 ApplicationContext를 만든다. 이 ApplicationContext는 Root WebApplicationContext를 상속한다.

### AnnotationConfigWebApplicationContext

```xml
<!--web.xml-->
<context-param>
    <param-name>contextClass</param-name>
    <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
</context-param>
```

여기서 contextClass는 ContextLoader의 예약어이다. ContextLoader는 contextClass라는 이름을 가진 param을 읽어온다. 그래서 @Configuration annotation이 붙은 함수를 읽어와서 설정파일로 만들어 준다. 

```java
//ContextLoader.java
...
protected Class<?> determineContextClass(ServletContext servletContext) {
    String contextClassName = servletContext.getInitParameter("contextClass");
    if (contextClassName != null) {
        try {
            return Cla...
```



<br>

### contextConfigLocation

```xml
<!--web.xml-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>me.jjjpark.AppConfig</param-value>
</context-param>
```

말 그대로 Context의 Config파일의 위치를 알려주는 것이다.

```java
//ContextLoader.java
...
    wac.setServletContext(sc);
    configLocationParam = sc.getInitParameter("contextConfigLocation");
    if (configLocationParam != null) {
    	wac.setConfigLocation(configLocationParam);
    }
```



## 불편한 점

이제 Servlet이 불편한 점은 /url 하나가 추가될 때마다 서블릿 설정이 추가된다.

```xml
<servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>me.jjjpark.HelloServlet</servlet-class>
</servlet>
  
<servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

