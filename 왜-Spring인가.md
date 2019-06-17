---
title: 왜 Spring인가?
date: 2019-03-05 20:31:56
tags:
categories: spring framework
---

1. 라이브러리 사용을 위해 jar을 직접 넣어주지 않고, maven이나 gradle로 관리한다.

2. 계정 정보와 같은 설정정보들을 servlet-context.xml에서 공통으로 관리할 수 있다.

   - servlet-context.xml - web application요청을 받기 위한 controller나 view(View Resolver)를 어떻게 처리할 것인지 설정하는 파일

   - root-context.xml - Business layer를 관리하기 위한 설정 (DB와 관련된 공통으로 사용할 수 있는 설정)

   - 위 두 context는 DispatcherServlet안에서 적용된다.

     ---

     ### DispatcherServlet

     > front controller pattern을 사용하여 처리 로직을 공유한다. 실제 일은 설정할 수 있는 대표 component에 의해서 수행된다. 그래서 DispatcherServlet는 xml이나 java로 선언이 되어져야 한다.

     DispatcherServlet는 WebApplicationContext( ApplicationContext가 확장된 것)으로 설정한다. WebApplicationContext는 연관된 Servelet Context, Servlet사이의 링크를 가지고 있다.  대부분의 application은 WebApplicationContext하나만으로 충분하다. 하지만 좀 자세히 들여다 보면 **Root WebApplicationContext**가 있다.

     **Root WebApplicationContext**는 다른 여러 DispatcherServlet와 공유해서 사용할 수 있고, 자식 WebApplicationContext를 가질 수도 있다.

     **Root WebApplicationContext**는 보통 repositories, business services와 같은 다른 다양한 Servlet과 공유할 수 있는 Servlet을 가지고 있다.

     

     ### 1.1.3 Web MVC Config

     DispatcherServlet은 request를 처리해서 적절한 response로 넘겨줄 특별한 beans이 있다.

     Application은 request를 처리하기 위해 Special Bean Type의 Beans을 선언한다. DispatcherServlet은 special bean을 WebApplicationContext에서 찾고, 없으면 ```DispatcherServlet.properties```에 있는 것을 반환한다.

     

     ### 1.1.4. Servlet Config

     ### 1.1.5. Processing

     DispatcherServlet는 다음과 같이 동작한다:

     - WebApplicationContext에서 요청과 관련된 controller나 다른 elements를 찾는다.

## 다시 돌아와서

서블릿 컨테이너 ( 웹 컨테이너, WAS, tomcat )

> 서블릿을 웹서버에 올리기만 하면 처리할 수 없는데, 이 서블릿을 관리해주는 컨테이너가 서블릿 컨테이너 이다. 서블릿 컨테이너는 서블릿 클래스의 인스턴스화, 초기화를 시킨다. 그리고 clinent의 요청에 따라 적절한 servlet을 mapping시켜준다. (이때 web.xml을 참고하여 적절한 servlet를 mapping시켜준다.) servlet이 사망하면 GC를 실행시켜준다.

또 Tomcat이 우리가 작성한 java파일을 class로 만들어서 메모리에 올려준다.

서블릿 컨테이너는 요청이 올떄마다 서블릿을 생성하는것이 아니라, 기존에 만들어 졌던것을 다시 사용한다. 

그리고 요청마다 쓰레드를 생성하기 때문에 동시에 여러 클라이언트의 요청을 처리할 수 있다. 

서블릿은 처음에 딱한번 init() 메소드를 실행한다. 그래서 딱 한번만 실행해도 되는 것은 여기에서 실행한다. 웹 컨테이너(톰캣)을 이용할 때, 첫 요청의 init()이 굉장히 오래걸릴 수 있어서 웹 컨테이너가 로드될때init()작업을 실행해줄 수 있는데, 이때 web.xml에 설정해주면 된다. web.xml은 웹 컨테이너가 적절한 servlet를 찾을 수 있도록 하는 xml파일이다.



## Spring Container(IoC Container, DI Container…)

![image](https://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile24.uf.tistory.com%2Fimage%2F2236F14757BBD251199156)



이처럼 SpringMVC도 Servlet Container가 관리하는 servlet이다.SpringMVC로의 모든 요청과 응답은 DispatcherServlet이 처리한다.  (front controller parttern) 

1. URL요청이 온다.
2. DispatcherServlet은 HandlerMapping를 통해 알맞은 Handler를 찾는다. 
3. HandlerAdapter로 찾은 Handler를 실행 시켜준다.

POJO와 configuration들을 Spring Container에 주입하면 POJO는 Bean으로 등록된고 사용할 수 있게 된다. 여기서 Bean은 싱글톤이다.

Spring Container는 2가지 유형의 Container를 제공한다. 

1. BeanFactory
2. ApplicationContext -> BeanFactory (ApplicationContext는 BeanFactory를 상속받는다.)

**BeanFactory**는 applicationContext.xml에 있는 bean들을 생성하고 관리한다. 또한 clinent로 부터 요청이 들어올때 객체를 생성한다.( Lazy-loading )

이를 확장한 것이 **ApplicationContext** **Container**이다. 트랜잭션관리나 다국어처리와 같은 확장된 기능을 담당한다.

즉, Spring Container는 xml에 있는 Bean 설정들을 이용해 Bean을 생성하고 관리한다.



### IoC란 제어의 역전이다. 왜 제어의 역전일까? 

기존에 로봇 class안에 로봇이 공격할 수 있는 패턴에 대한 함수들이 있었다. 그래서 사용자가 새로운 공격을 할려면 **직접** 함수를 추가해 주어야 했다. 하지만 IoC는 이미 공격 전략들을 다 만들어 놓고, applicationContext라는 곳에 공격전략(beans.xml)을 넣어주면 applicationContext안에서 생성자를 이용하거나, 혹은 setter를 이용해서 알아서 다 만들어준다. 그리고 사용자의 applicationContext의 getBean이라는 메소드를 이용하여 이미 만들어진 Bean을 사용할 수 있기 떄문에 제어의 역전이라고 부른다.



### DI - 의존성 주입

의존성이란? class A 내부에서 ```new B()```로 새로운 객체를 만들어 주었다면 둘은 의존관계를 갖는다. 이 의존성을 내부가 아닌 외부에서 넣어주는 것을 의존성 주입이라고 한다. 의존성을 주입하는 방법에는 여러가지가 있다.

1. xml을 이용한 주입
   - 생성자를 이용
   - setter method를 이용
2. @Autowired annotation을 이용한 주입

그렇다면 DI는 왜 하는 것일까?



