---
title: Dependency Injection(java config)
date: 2018-11-29 13:13:55
tags:
categories: spring framework
---



DI(의존성 주입)을 적용하는 방법은 [xml을 이용한 DI](https://jjjpark.github.io/2018/11/28/Dependency-Injection-DI/) 뿐만아니라 java config파일을 만들어서 DI를 적용시키는 방법이 있다.



## Java config를 이용한 방법

### ApplicationConfig.java

```java
package kr.or.connect.diexam01;
import org.springframework.context.annotation.*;

@Configuration
public class ApplicationConfig {
	@Bean
	public Car car(Engine e) {
		Car c = new Car();
		c.setEngine(e);
		return c;
	}
	
	@Bean
	public Engine engine() {
		return new Engine();
	}
}
```

@Configuration - spring에서 사용하는 설정 클래스

@Bean - bean으로 등록하겠다.(bean은 컴파일 시간에 객체가 만들어져서 싱글톤으로 관리된다.)



### ApplicationContextExam03.java

```java
package kr.or.connect.diexam01;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ApplicationContextExam03 {

	public static void main(String[] args) {
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig.class);
		   
		Car car = (Car)ac.getBean("car");
		car.run();
		
	}
}
```



하지만, 

## 더 간단한 방법이 있다.



### ApplicationConfig2.java

```java
package kr.or.connect.diexam01;
import org.springframework.context.annotation.*;

@Configuration
@ComponentScan("kr.or.connect.diexam01")
	public class ApplicationConfig2 {
}
```

@ComponentScan("kr.or.connect.diexam01") - 명시된 package를 돌면서 @Component가 붙은 것을 bean으로 만든다.



### Engine.java

```java
package kr.or.connect.diexam01;

import org.springframework.stereotype.Component;

@Component
public class Engine {
	public Engine() {
		System.out.println("Engine 생성자");
	}
	
	public void exec() {
		System.out.println("엔진이 동작합니다.");
	}
}
```



### Car.java

```java
package kr.or.connect.diexam01;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Car {
	@Autowired
	private Engine v8;
	
	public Car() {
		System.out.println("Car 생성자");
	}
	
	public void run() {
		System.out.println("엔진을 이용하여 달립니다.");
		v8.exec();
	}
}
```

@Autowired - Engine이라는 bean이 있으면 현재 클래스에 주입한다.(DI 적용)



### ApplicationContextExam04.java

```java
package kr.or.connect.diexam01;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ApplicationContextExam04 {

	public static void main(String[] args) {
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig2.class);
		   
		Car car = ac.getBean(Car.class);
		car.run();
		
	}
}
```



물론, [간단한 방법이 ](##더-간단한-방법이-있다.) 편하지만, @ComponentScan은 @Component가 달려있어야 Bean으로 만든다. 그래서 외부라이브러리에는 @ComponentScan을 사용할 수 없다. 그럴때는 [Java Config](## Java config를-이용한-방법) 방법을 사용한다.

