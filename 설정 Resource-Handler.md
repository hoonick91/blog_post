---
title: Resource Handler
typora-copy-images-to: Resource Handler
date: 2019-05-17 08:17:43
tags:
categories: spring framework
---

Html, img, css와 같은 정적파일들을 제공하는 것.

정적인 자원을 처리하는 default Servlet이 있다.

Spring은 default



```java
import org...

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/mobile/**")
                .addResourceLocations("classpath:/mobile/")
//                .setCacheControl(CacheControl.maxAge(10, TimeUnit.MINUTES)) // resource를 10분동안 캐싱한다.
                .resourceChain(true);
    }
}
```

WebConfig를 위와 같이 설정하여 Resource handler를 사용할 수 있다. Cache 여부도 설정할 수 있다. 이는 web에서 정적파일 요청이 왔을때, 기존에 있는걸 캐싱해놓으면, 304를 반환하여 기존의 정적파일과 변화가 없음을 알려준다. 



이걸 왜사용하느냐면, 정적파일에 대한 캐싱여부 등 Resource에 대한 handling을 하려고 쓰는 것같다.