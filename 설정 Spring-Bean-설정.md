---
title: Bean 설정
date: 2019-03-07 18:10:01
tags:
categories: spring framework
---

기존 설정은 WebConfig.java에 @Bean을 이용하여 직접 handelerMapping, handlerAdaptor 설정했다. WebConfig에 따로 설정하지 않으면, dispatcherServlet.properties에서 가져온다.

```java
@Configuration
@ComponentScan
public class WebConfig {
    @Bean
    public HandlerMapping handlerMapping() {
        RequestMappingHandlerMapping handlerMapping = new RequestMappingHandlerMapping();
        handlerMapping.setInterceptors(); // 각각의 설정들
        return handlerMapping;
    }
}
```

@EnableWebMvc를 이용하면 기본적인 설정이 들어있다. (Delegation 구조로 되어있다.)

```java
@Configuration
@ComponentScan
@EnableWebMvc
public class WebConfig {
    
}
```

이는 Delegation구조로 되어 있기 때문에 사용자에게 WebMvcConfigurer라는 인터페이스를 제공한다. 사용자는 그래서 자유롭게 조작할 수 있다.

```implement WebMvcConfigurer``` Spring boot에서도 사용한다.