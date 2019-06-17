---
title: HTTP 메시지 컨버터
typora-copy-images-to: HTTP 메시지 컨버터
date: 2019-05-17 18:37:03
tags:
categories: spring framework
---

> HTTP Message converter는 @RequestBody로 들어온 객체에 대해 json, xml 등의 형식으로 convert해준다. 그리고 반환하는 값도 또 한 convert할수 있다. 스프링 부트는 Jackson2를 기본적으로 추가해준다. (ObjectMapper)

### SampleController.java

```java
import org.springframework.web.bind.annotation.*;

@RestController
public class SampleController {
    @GetMapping("/jsonMessage")
    public Person jsonMessage(@RequestBody Person person) {
        return person;
    }
}
```



### SampleControllerTest.java

```java
import com.fas..
  
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Autowired
    PersonRepository personRepository;

    @Autowired
    ObjectMapper objectMapper;

  	@Test
    public void jsonMessage() throws Exception {
        Person person = new Person();
        person.setId(2019l);
        person.setName("jonghoon");

        String jsonString = objectMapper.writeValueAsString(person);

        this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .accept(MediaType.APPLICATION_JSON_VALUE)
                .content(jsonString))
                .andDo(print())
                .andExpect(status().isOk());
    }
}
```

this.mockMvc.perform()에 HTTP요청에 대한 정보가 들어간다. Content-type요청이 보내는 body의 형식을 의미한다. 여기서는 application/json이다. 그리고 .accept()는 요청이 결과로 얻고 싶은 데이터의 형식을 의미한다.

이게 왜 중요하냐면 spring boot는 이 형식을 보고 어떤 converter를 bean으로 등록해줄지 결정하기 때문이다.

- MvcConfigurationSupport : 각 converter가 있으면 알아서 messageConverter를 등록해준다. classpath에 라이브러리가 있는 경우에만. 



