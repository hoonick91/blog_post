---
title: spring MVC 설정 정리
typora-copy-images-to: spring MVC 설정 정리
date: 2019-05-20 08:43:23
tags:
categories: spring framework
---

우리가 SpringMVC를 사용하려면 여러가지 필요한 설정들이 있다. 예를들어

- 요청에 대해 body를 객체로 변환한다.(MessageConverter)
- 요청에 대해 미리 처리를 한다…(HandlerInterceptor)
- 등등

이를 위해서는 handlerInterceptor을 HandlerMapping에 등록해주어야 하고, messageConverter를 HandlerAdapter에 등록해야한다. 

이런걸 쉽게 작성할수 있게끔 @EnableWebMvc 를 사용한다. 이는 기본적인 Spring MVC에서 제공하는 설정을 사용하지 않는 다는 뜻이다 경우에 따라 커스터마이징 하고싶은 경우가 있는데, 그때 WebMvcConfigurer을 implements해서 구현 한다. 

Spring boot는 자동설정으로 인해 다양한 기능이 제공된다. JSON지원,(HttpMessageConverter가 자동으로 등록) Spring boot도 마찬가지고 WebMvcConfigurer을 통해 커스터마이징할 수 있다. 하지만 @EnableWebMvc를 사용하면 Spring boot의 설정을 아무것도 사용하지 않게 된다. 우선은 application.properties에서 key/value를 조정해서 설정을 조절할 수 있는지 먼저 확인한다. 직접 @Bean을 등록을 할 수도 있지만 그렇게 보통하지는 않는다. 

1. application.properties
2. WebMvcConfigurer
3. @Bean으로 직접 설정값을 등록