---
title: Dependency Injection(DI)
date: 2018-11-28 18:43:30
tags:
categories: spring framework
---



 객체간의 의존관계를 맺어준다는 의미이다. 그걸 개발자가 해주는것이 아니라 spring IoC 컨데이너에게 제어권을 넘겨 스스로 관계를 맺게 한다.



### Engine.java

```java
package kr.or.connect.diexam01;

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

public class Car {
	Engine v8;
	
	public Car() {
		System.out.println("Car 생성자");
	}
	
	public void setEngine(Engine e) {
		this.v8 = e;
	}
	
	public void run() {
		System.out.println("엔진을 이용하여 달립니다.");
		v8.exec();
	}
}
```



## Spring 사용 전 :

### Main.java

```java
public static void main(){
	Engine e = new Engine();
	Car c = new Car();
	c.setEngine( e );
	c.run();
}
```





##  Spring 사용 후 :

.xml

```xml
<bean id="e" class="kr.or.connect.diexam01.Engine"></bean>
<bean id="car" class="kr.or.connect.diexam01.Car">
	<property name="engine" ref="e"></property>
</bean>
```

위의 xml설정은 다음과 같은 의미를 갖는다.

```java
Engine e = new Engine();
Car c = new Car();
c.setEngine( e );
```



### ApplicationContextExam02.java

위의 xml 설정파일을 읽어서 실행하는 부분

```java
package kr.or.connect.diexam01;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ApplicationContextExam02 {

	public static void main(String[] args) {
		ApplicationContext ac = new ClassPathXmlApplicationContext( 
				"classpath:applicationContext.xml"); 

		Car car = (Car)ac.getBean("car");
		car.run();
		
	}
}
/*
##Result##
Engine 생성자
Car 생성자
엔진을 이용하여 달립니다.
엔진이 동작합니다.
*/
```



작성된 xml은 컴파일 타임에 spring이 싱글톤으로 객체를 생성해준다.

```java
/*
Engine 생성자
Car 생성자
*/
```

그리고, Car.run() 메소드를 호출하면 나머지 결과가 나온다.



이렇게 하면 실행클래스의 코드는 Car라는 클래스만 알아도 어떤 엔진을 사용할지 몰라도 된다. 



## Reference

[https://www.edwith.org/boostcourse-web/lecture/20657/](https://www.edwith.org/boostcourse-web/lecture/20657/)

