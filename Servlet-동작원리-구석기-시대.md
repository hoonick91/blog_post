---
title: Servlet 동작원리 구석기시대
date: 2019-03-08 19:14:58
tags:
categories: spring Framework
---

> **IDE** - intellij, **Build tool** - maven, **Archetype** - maven-webapp

# Servlet project



1. pom.xml에 우리가 사용할 java servlet을 추가 해주었다.

   ```xml
   <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
       <dependency>
         <groupId>javax.servlet</groupId>
         <artifactId>javax.servlet-api</artifactId>
         <version>4.0.1</version>
         <scope>provided</scope>
       </dependency>
   ```

   

2. Main-java-me.jjjpark 패키지를 만든다. 그리고, HelloServlet를 작성해 주었다.

   ```java
   public class HelloServlet extends HttpServlet {
   
       @Override
       public void init() throws ServletException {
           System.out.println("init()");
       }
   
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           System.out.println("doGet");
           resp.getWriter().println("<html>");
           resp.getWriter().println("<body>");
           resp.getWriter().println("<h1>Hello Servlet~!</h1>");
           resp.getWriter().println("</body>");
           resp.getWriter().println("</html>");
       }
   
       @Override
       public void destroy() {
           System.out.println("destroy()");
       }
   }
   
   ```

   여기서 init(), destroy() method는 HttpServlet의 super class인 **GenericServlet**의 메소드 이다. **HttpServlet**은 **GenericServlet**에서 Http기능을 추가해 구현한 클래스이다.

   

3. 방금 만든 Servlet을 web.xml에 등록해준다.

   ```xml
   <!DOCTYPE web-app PUBLIC
    "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd" >
   
   <web-app>
     <display-name>Archetype Created Web Application</display-name>
     <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>me.jjjpark.HelloServlet</servlet-class>
     </servlet>
     
     <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
     </servlet-mapping>
   </web-app>
   ```



그리고 실행한 후 http://localhost:8080/hello로 요청을 보내면 

```java
...
init() // 처음에 한번 생성된다.
doGet()
destory() // when tomcat shutdown
```

- ?? SrpingMvc lifecycle 도는 코드 보기



## Servlet Listener & Servlet Filter



![image](https://github.com/jjjpark/draw-io-images/blob/master/listenerFilter.png?raw=true)



### Servlet Listener

- Context의 attribute, life cycle의 변화에 따라 이벤트를 발생 시킨다.
- 여러 Servlet에서 공통적으로 사용되는 것을 Servlet Context에 넣어 놓고, servlet이 가져다 쓸 수 있다. 
- Servlet Container가 구동되고, Servlet Context가 만들어지면 Servlet Listener의 ```contextInitialized(ServletContextEvent sce)```가 실행된다. 그리고 servlet이 destroyed된 후에 ```contextDestroye(ServletContextEvent sce)``` 가 실행된다. 이 처럼 Listener는 Context의 상태를 보고 있다가 변화가 감지되면 이벤트를 발생시킨다. 그래서 이때, Context에 param같은 것을 설정해줄 수 있게 된다.



1. MyListener.java를 만든다.

   ```java
   public class MyListener implements ServletContextListener {
       @Override
       public void contextInitialized(ServletContextEvent sce) {
           System.out.println("Context Initialized");
           sce.getServletContext().setAttribute("name", "jhoon");
       }
   
       @Override
       public void contextDestroyed(ServletContextEvent sce) {
           System.out.println("Context Destroyed");
       }
   }
   ```

2. web.xml에 등록한다.

   ```xml
   <!DOCTYPE web-app PUBLIC
    "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd" >
   
   <web-app>
     <display-name>Archetype Created Web Application</display-name>
   
     <listener>
       <listener-class>me.jjjpark.MyListener</listener-class>
     </listener>
     
     <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>me.jjjpark.HelloServlet</servlet-class>
     </servlet>
     
     <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
     </servlet-mapping>
   </web-app>
   ```

   

### Servlet Filter

- 아무것도 안해도 Filter는 init이 된다.
- 사용자한테 요청이 들어왔을 때, Servlet의 init() method를 실행 시키고 Filter갔다가 doGet(Servlet)에게 간다.
- Servlet, Filter, Listener 순으로 detroyed 된다.
- filter-mapping를 통해 여러 Servlet에 Filter를 적용할 수 있고, url-pattern을 통해 url을 기준으로 삼을 수 있다.( 여러개의 servlet에 filter를 적용해야 할 경우 이용한다. )
- vs HandlerAdapter - interceptor



만들기 순서 (구동 순서 아님)

1. MyFilter.java를 만든다.

   ```java
   public class MyFilter implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
           System.out.println("Filter init");
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("Filter");
           filterChain.doFilter(servletRequest, servletResponse);
       }
   
       @Override
       public void destroy() {
           System.out.println("Filter Destroy");
       }
   }
   ```

   doFilter에서 Filter는 chainning하여 실행되므로 다음 Filter에게 param을 넘겨야 한다.

   

2. web.xml에 추가한다.

   ```xml
   <!DOCTYPE web-app PUBLIC
    "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd" >
   
   <web-app>
     <display-name>Archetype Created Web Application</display-name>
   
     <filter>
       <filter-name>myFilter</filter-name>
       <filter-class>me.jjjpark.MyFilter</filter-class>
     </filter>
   
     <filter-mapping>
       <filter-name>myFilter</filter-name>
       <servlet-name>hello</servlet-name>
     </filter-mapping>
   
     <listener>
       <listener-class>me.jjjpark.MyListener</listener-class>
     </listener>
     
     <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>me.jjjpark.HelloServlet</servlet-class>
     </servlet>
     
     <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
     </servlet-mapping>
   </web-app>
   ```



### Servlet Context

- Servlet보다 먼저 생성된다. 그리고 나중에 destoryed 된다. 여러 Servlet에서 사용되는 공통적인 것을 모아 놓는다. 가령 DB Connection같은 경우
- Servlet는 HttpServlet을 상속하고, HttpServlet은 GenericServlet을 상속하는데 GenericServlet는 getServletContext()를 구현하고 있어서 Servlet에서 getServletContext() 메소드를 사용할 수 있다.
- GenericServlet이 init될 때, ServletConfig를 parameter로 받아 Config를 저장한다.
