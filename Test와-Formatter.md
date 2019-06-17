---
title: Test와 Formatter
typora-copy-images-to: Test와 Formatter
date: 2019-05-16 00:02:31
tags:
categories: spring framework
---



### SampleController.java

```java
import org.springfram...

@RestController
public class SampleController {

    @GetMapping("/hello")
    public String hello(@RequestParam("name") Person person){
        //formatter의 역할을 name이라는 글자를 Person으로 넣어주는 것이다.
        return "hello " + person.getName();
    }
}
```



### SampleControllerTest.java

```java
package me.jjjpark.demobootweb;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.junit.jupiter.api.Assertions.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;

@RunWith(SpringRunner.class)
//@WebMvcTest //@Component 떄문에 다른걸 사용해야 한다.
@SpringBootTest
@AutoConfigureMockMvc //MockMvc가 자동을 등록되지 않으므로 추가
public class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        this.mockMvc.perform(get("/hello")
                .param("name","jjjpark")) //queryString (name=jjjpark)
                .andDo(print()) //결과를 출력한다.
                .andExpect(content().string("hello jjjpark")); //예상하는 결과
    }
}
```



### PersonFormatter.java

```java
import org.springframework.format.Formatter;
...
  
@Component //Web과 관련된 bean이라고 인식 못하기떄문에 Test가 깨진다.(@WebMvcTest 때문에)
public class PersonFormatter implements Formatter<Person> {

    @Override
    public Person parse(String s, Locale locale) throws ParseException {
        Person person = new Person();
        person.setName(s);
        return person;
    }

    @Override
    public String print(Person person, Locale locale) {
        return person.toString();
    }
}
```



### Spring data JPA - 이런게 있더라~