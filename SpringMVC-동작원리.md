---
title: SpringMVC 동작원리
typora-copy-images-to: SpringMVC 동작원리
date: 2019-03-31 14:32:19
tags:
categories: spring framework
---

Spring Boot

Servlet 기반 application이다.

- init
- doGet, doPost
- destory
  - DispatcherServlet
    - 초기화시 여러 인터페이스들을 이용한다. (빈을 찾아서 전략으로 사용한다.) 없다면 DispatcherServlet.properties에 있는 기본전략을 사용한다.
    - web.xml이 없어도 구현이 가능하다.

Servlet Container가 필요하다.

1. 요청이 DispatcherServlet으로 들어온다.
2. interface전략을 사용해서 초기화한다.(각 요청에 대한 bean)
3. handlerMapping을 이용하여 handler를 찾아준다.
4. handlerAdapter를 이용하여 handlerMapping으로 찾아준 handler를 실행시켜준다.
   - 이때 reflection을 이용하여 사용된다.

Spring boot를 사용하지 않으면 @Bean에 들어가는 메소드가 중요하다. 하지만 기본 @Bean이 없다면 DispatcherServlet.properties에 있는 설정들이 들어간다. ViewResolver같은 것을 스스로 등록해야한다. 



Spring boot를 사용하면, java application안에 imbedded tomcat에 servlet을 등록해준다. 기본적인 전략도 boot가 등록해준다. spring boot는 더 많은 것들을 기본 @Bean으로 등록해논다. 가령 ViewResolver와 같은 경우



## HandlerMapping

> ## 초기에 initHandlerMappings로 Bean에 등록된 모든 HandlerMapping을 가져온다.

handlerMapping이 등록된 클래스를 돌면서 RequestMappingHandlerMapping.getHandler(request)를 요청한다. 각 클래스(RequestMappingHandlerMapping -> RequestMappingInfoHandlerMapping -> AbstractHandlerMethodMapping<RequestMappingInfo> -> AbstractHandlerMapping)

```java
@Override
@Nullable
public final HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
...
		HandlerExecutionChain executionChain = getHandlerExecutionChain(handler, request);
...
}
```

```java
protected HandlerExecutionChain getHandlerExecutionChain(Object handler, HttpServletRequest request) {
		HandlerExecutionChain chain = (handler instanceof HandlerExecutionChain ?
				(HandlerExecutionChain) handler : new HandlerExecutionChain(handler));

		String lookupPath = this.urlPathHelper.getLookupPathForRequest(request);
		for (HandlerInterceptor interceptor : this.adaptedInterceptors) {
			if (interceptor instanceof MappedInterceptor) {
				MappedInterceptor mappedInterceptor = (MappedInterceptor) interceptor;
				if (mappedInterceptor.matches(lookupPath, this.pathMatcher)) {
					chain.addInterceptor(mappedInterceptor.getInterceptor());
				}
			}
			else {
				chain.addInterceptor(interceptor);
			}
		}
		return chain;
	}
```

BeanFactory에 있는 interceptor들은 ```this.adaptedInterceptors```에 들어있다. 이 인터셉터들이 사용자가 요청한 url과 맞는지 확인하여 맞으면 HandlerExceptionChain을 반환한다. 아마 Beanfactory에는 @Controller, @RequestMapping으로 만들어진 Bean들이 있을 것같다. 결국 반환된 HandlerExceptionChain은 DispatcherServlet에서 null인지 아닌지 최종 검사를 하게 되고, 다시 그 ```HandlerExceptionChain.getHandler()``` 로 반환된 값을 HandlerAdapter의 param으로 넘김다. 

여기 HandlerExceptionChain에 무언가 chain적인 비밀이 있는 것 같다.



## HandlerAdapter

> 초기에 Bean에 등록된 모든 HandlerAdapter을 가져온다.

handlerAdapter중에 for문 돌면서 해당 요청에 대한 handlerMapping이용해 찾은 handler를 처리할 수 있는 HandlerAdapter을 찾고, 찾으면 handlerAdapter.handle를 호출한다. (```mv = ha.handle(processedRequest, response, mappedHandler.getHandler())```)

