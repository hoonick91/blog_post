---
title: MVC 빈 설정
typora-copy-images-to: [1단계] MVC 빈 설정
date: 2019-05-12 19:58:56
tags:
categories: spring framework
---

![image](https://github.com/jjjpark/draw-io-images/blob/master/dispatcherServlet.png?raw=true)

위의 그림에서 처럼 요청을 처리하는데 HandlerMapping, HandlerAdapter가 사용되는 것을 볼수 있다. 여기에 사용자는 요청을 어떻게 처리할 것인지 설정할 수 있다. 예를 들어 HM(handlerMapping)에 interceptor를 붙여 

> WebConfig에 HandlerMapping, HandlerAdapter와 같은 설정들을 해줄 수 있다. 하지만 boot는 물론이고, MVC에서도 이런식으로 설정해주지는 않는다. 너무 복잡하다.

```java
import org...

@Configuration
@ComponentScan
public class WebConfig  {

    @Bean
    public HandlerMapping handlerMapping() {
        RequestMappingHandlerMapping handlerMapping = new RequestMappingHandlerMapping();
        handlerMapping.setInterceptors();
        handlerMapping.setOrder(Ordered.HIGHEST_PRECEDENCE);// 우선순위가 높은 Handler을 찾아온다.
        return handlerMapping;
    }

    @Bean
    public HandlerAdapter handlerAdapter(){
        RequestMappingHandlerAdapter handlerAdapter = new RequestMappingHandlerAdapter();
        return handlerAdapter;
    }

    @Bean
    public ViewResolver viewResolver(){
       ...
    }
}
```

 

그래서 나오게 된 것이 바로 이것이다.

```java
package me.jjjpark;

import...

@Configuration
@ComponentScan
@EnableWebMvc
public class WebConfig  {
    @Bean
    public ViewResolver viewResolver(){
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB-INF/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```

@EnableWebMvc - @Import(DelegatingWebMvcConfiguration.class) - WebMvcConfigurationSupport

- 이 파일에서는 HandlerMapping, HandlerAdapter에 대한 설정을 할 수 있다. HandlerAdapter에서는 messageConverter같은 것을 설정해준다.(Jackson,)

- HandlerMapping의 RequestMappingHandlerMapping의 order을 (0)으로 바꿔 줌 (BeanNameUrlHandlerMapping을 잘 사용하지 않게 된다.)

- Intercepter를 추가해 줌

  ```java
  @Bean
  	public RequestMappingHandlerMapping requestMappingHandlerMapping() { 
      //annotation을 이용한 handlerMapping 
  		RequestMappingHandlerMapping mapping = createRequestMappingHandlerMapping();
  		mapping.setOrder(0);
  		mapping.setInterceptors(getInterceptors());
  		...
  ```

- 동적으로 Message Converter가 추가된다.

- Delegation 구조…? 이기 때문에 다른 구조에서 내가 원하는 것만 추가할 수 있다.?..?조금만 수정..? 어떻게하냐면 다음 수업시간에 한다.

  - 확장할 수 있도록 되어 있는데 이때 interface를 implements 해야한다. (WebMvcConfigurer)



이렇게 Spring을 설정하는 방법에는 크게 3가지가 있다.

1. @Configuration을 이용하여 직접설정하는 방법
2. @EnableWebMvc을 이용하여 기본 설정은 가져오는 방법
   - Implements WebMvcConfigurer을 해서 추가 설정을 할 수 있다.
3. Spring boot를 사용하는 방법
   - @Configuration + Implements WebMvcConfigurer: 스프링 부트의 자동 설정 + 추가적인 설정
   - @Configuration + @EnableWebMvc + Imlements WebMvcConfigurer: 스프링 부트의 Spring MVC 자동 설정 사용하지 않음

> 지금까지의 spring설정 3가지 방법으로 주로 어떤 설정을 살펴보게 되는지 알아보기 예를들어 intercepter? messageConverter?



jar - 독립적인 java application으로 실행
war - tomcat과 같은 webserver에 배포 









