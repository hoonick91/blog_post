---
title: SpringMVC 청동기시대
date: 2019-03-15 08:20:47
tags:
categories: spring framework
---

## DispatcherServlet.java

> 기존의 URL 요청하나마다 Servlet을 만들어줘야 하는 번거로움이 있었는데, 이를 해결한것이 dispatcherServlet이다. 모든 URL요청을  dispatcherServlet에 넘기고 각 요청에 맞는 결과를 반환한다.

<img src="https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg" width="450px"/>

출처: [https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg](https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg)

```xml
<!--web.xml-->
<servlet>
    <servlet-name>app</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextClass</param-name>
      <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
    </init-param>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>me.jjjpark.WebConfig</param-value>
    </init-param>
</servlet>
```



> 요청 처리과정

1. 요청을 분석해서 multipart인지? 뭐 다른 요청인지 요청을 분석한다.

   ```java
   //dispatcherServlet.java
   protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
   ...
   try {
      processedRequest = checkMultipart(request);
      multipartRequestParsed = (processedRequest != request);
   ...
   ```

2. 요청을 처리할 수 있는 핸들러를 찾아온다.(handlerMapping - interface)

   요청이 /app/hello라고 했을때, 이 요청은 @RequestMapping의 @GetMapping("/hello")에서 처리할 수 있다. 그래서 RequestMappingHandlerMapping이 처리할 것이다.

   - BeanNameUrlHandlerMapping
   - RequestMappingHandlerMapping - @Controller안의 @RequestMapping이 붙은 method-level로 부터 **RequestMappingInfo** instance를 생성한다.

3. handler를 실행할 수 있는 handlerAdapter을 찾아온다.

   - HttpRequestHandlerAdapter
   - SimpleControllerHandlerAdapter
   - RequestMappingHandlerAdapter

4. java의 reflection을 이용하여 handlerMethod를 실행 ```invokeHandlerMethod(request, response, handlerMethod)```

5. Return값을 처리할 수 있는, HandlerMethodReturnValueHandler를 찾는다.



### View가 있을 경우 (DispatcherServlet 2부)