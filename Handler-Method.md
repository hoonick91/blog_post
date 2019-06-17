---
title: Handler Method
typora-copy-images-to: Handler Method
date: 2019-05-24 08:07:14
tags:
categories: Spring framework
---



## Handler Method(Controller method)

### 1. Argument & Return type

#### Argument

- NativeWebRequest, 
- WebRequest
- HttpServletRequest, HttpServletResponse
  - RequestBody에 있는 거는 request.getReader(InputStream requestBody, OutputStream responseBobyd)
- PushBuilder - 일반적으로 한번 요청이 들어왔을때, 보여주는 브라우져는 필요한 img를 다시 요청한다. 하지만 PushBuilder를 사용하면 서버가 능동적으로 img를 반환한다.
- HttpMethod - 요청의 http method를 알 수 있다.
  - @RequestMethod를 사용할 때, 모든 method를 사용할 수 있으므로
- Locale, TimeZone, ZoneId - LocalResolver를 이용해서 처리함.

---

- @PathVariable
- @RequestPart - 파일 
- @ModelAttribute - 추후에 설명
- @SessionAttribute
- UriComponentBuilder - utility...
- @MatrixVariable - key/value 쌍을 URI에 적용하는 방안



#### Return type

- ResponseEntity<String> events() - 응답헤더 , 응답 코드에 대한 것을 추가해줄 수 있다. Rest…?
- HttpHeaders
- View - viewResolver를 타지않고, view를 찾는다.
- Model - model정보만 넘긴다. 그래서 view를 모르니까 view를 유추한다…?



### 2. URI 패턴

- @PathVariable
- @MatrixVariable



### 3. 요청매개변수

- queryParm
  - @RequestParam(생략도 가능)
- formData



### 4. 폼 서브밋

### 5. @ModelAttribute

- 요청에 들어있는 data를 가져온다.
- /event?name=jjjpark
- /event/name/{name} 등 여러가지 바인딩이 가능하다.

### 6. @Validated

- 그룹으로 validation 체크를 할 수 있다.

### 7. 폼 서브밋 에러

### 8. @SessionAttributes

자동으로 model에 있는 attribute중에 @SessionAttributes("") ""에 선언된 attribute를 session에 넣어준다.

- 장바구니 데이터
- 데이터 생성시 여러화면에 거쳐서 만들게 되는 경우
- 데이터가 생성될때(POST)일때 데이터가 생성되는 시간을 넣어줄 수 도 있을것 같다...

SessionStatus를 handler의 param으로 주어 session의 정리를 할 수 있다. 예를 들어 여러페이지에 걸쳐서 만들어진 데이터가 완전 저장되어 더이상 session에 갖고 있지 않아도 되는경우

- 비울때 sessionStatus.setComplete(); 를 사용할 수 있다.
- Session을 사용하여 사용자의 정보를 계속 담고 있을 수 있다.

### 10. @SessionAttribute

@SessionAttribute's'는 Controller class 안에서만 Session을 정의해서 사용한다. SessionStatus로 Session을 관리한다.(여러 Controller에서 적용되지는 않는다.)

@SessionAttribute는 범용적일떄 사용한다. Interceptor나 뭐 이럴 때… 한번 session에 기록된것은 변하지 않는다.



### 11. RedirectAttributes

form에서 값을 작성하고 다른 페이지로 데이터를 넘기는 방법으로는 (리다이렉트) model에 담은 후 Spring 설정값을 바꾸어 RequestParam에 나타나게한다. 하지만 이는 model에 있는 데이터를 모두 보여준다. 내가 보여주고 싶은 값만 바꾸고 싶으면 RedirectAttributes를 사용한다.

받을때는 @RequestParam, Model 둘중 선택해서 받으면 된다.

받을때 주의할점이 @ModelAttribute를 사용할경우 Controller 상단에서 선언한 @SessionAttributes의 ("value") 값과 @ModelAttribute로 선언한 "value"를 똑같은 이름으로 쓰면 안된다. @ModelAttriubte의 이름과 @SessionAttributes의 이름이 같다면 model을 Session에서 찾기 때문이다.

### 12. FlashAttributes

RedirectAttributes - queryParam으로 데이터를 전달하기 때문에 모두 문자열로 변환이 가능해야된다. 복합적이 객체전달은 어렵다.

FalshAttributes - 1회성으로 사용한다. 객체를 전달할 수 있다. httpSession으로 전달되기 때문에 URI경로에 노출되지 않는다.



### 13. MulipartFile

이걸 사용하려면 DispatcherServlet에 선언이 되어있어야 한다. MultipartResolver를 선언 즉, 선언하지 않으면 설정하지 않는다. 하지만 Spring boot는 설정이 되어있다. (MultipartAutoConfiguration) 

### 14. ResponseEntity

### 15. @RequestBody & HttpEntitiy

요청이 객체로 들어올때, 미리 만들어둔 객체로 형식을 바꿔줄 수 있는데, 이를 HttpMessageConverter가 역할을 한다.
@EnableWebMvc를 붙여야하는데, 이를 붙이는 순간 기본적인 컨버터들을 못쓴다. 요청의 헤더에 Content-type이 있는데 이를 보고 어떤 형식이다~ 라고 이해한 후 그에 맞는 컨버터를 이용한다.

BindingResult bindingResult를 붙이면 binding error가 생기면 우리가 원하는 처리를 할 수 있다. 무조건 400 error만 주는 것이 아니라… 좀 더 친절한 서버개발자가 된다.



### 16. @ResponseBody & ResponseEntity

@ResponseBody - HttpMessageConverter를 이용해서 응답 본문에 return값을 담아준다.

Accept - 반환을 원하는 리턴타입의 형식

Meta annotation - 어떤 annotation위에 붙어서 사용됨…?

- meta data - 데이터에 관한 데이터
  - meta는 어떤 추상화를 한것을 일컷는다. 
    - 그러므로 meta annotataion은 annotation에 대한 데이터를 말하고, 이는 예를 들어 @Service의 안에 meta annotation으로 있는 @Component를 들 수 있다.

ResponseEntity - 결과 값을 넣어줄 형식을 만들어 준다.

```java
@GetMapping
public ResponseEntity<Event> getEvent() {

  Event event = new Event();
  event.setName("hoon");
  event.setLimit(20);

  return new ResponseEntity<>(event, HttpStatus.OK);
}
```

다음과 같이 결과값에 상태코드를 넣어 줄 수 있다.
