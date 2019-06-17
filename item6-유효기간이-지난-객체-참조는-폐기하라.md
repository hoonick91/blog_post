---
title: item6. 유효기간이 지난 객체 참조는 폐기하라
date: 2019-03-25 11:17:38
tags:
categories: effective java
---

- 디스크 페이징

- obsolete reference

- # GC의 동작원리

  Stop-the-world : GC를 실행하기위해 다른 쓰레드들이 작업을 멈추는 것. 이 시간을 적게 하는 것이 성능을 나타낸다.

  GC는 2가지의 가설에 의해 설계 되었다.

   	1. 대부분의 객체는 금방 접근불가능상태가 된다. (객체의 유효기간이 짧다)
   	2. 오래된 객체는 새로 생긴 객체 참조를 적게한다.

  그래서 **HotSpot VM(JVM 종류 중 하나)**에서는 Old, Young영역으로 물리적 공간이 나뉘어 있다.

  

  ## Young영역

  > Eden, Survivor, Survivor

  

   	1. 새로생긴 객체는 eden에 저장된다.
   	2. GC로 Eden에서 살아남은 객체는 Survivor에 저장된다. 
   	3. 한쪽의 Survivor이 다 차면, 그 중 살아남은 객체는 다른 Survivor에 저장된다.
   	4. 혹은 Old Generation으로 이동한다. 이때 다 차있었던 Survivor은 비어있는 상태가 된다.

  

  ```markdown
  HotSpot VM에서는 빠른 메모리 할당을 위해 2가지를 사용한다.
  
  > Bump-the-pointer, TLABs(Thread-Local-Allocation-Buffers)
  
  Bump-the-pointer는 stack처럼 새로운 객체가 할당될때, 맨위에 있는 마지막 객체를 추적해서 Eden에 새로운 객체가 할당될 수 있는지 없는지만 판단한다. 하지만 이는 단일 쓰레드 환경일 때이다. 멀티쓰레드 환경일때는 TLABs를 이용한다.
  
  멀티쓰레드 환경일때에는 Eden에 저장하려면 lock가 걸릴 수 밖에 없다. 이를 해결한 것이 TLABs이다. 각각의 Thread가 Eden의 덩어리를 갖게 하는 것인데.. 그럼 동기화는 어떻게 하는 걸까?
  ```

  

  ## Old영역에 대한 GC

  > 기본적으로 데이터가 가득차면 GC를 실행한다. 방식은 5가지 방식이 있다.(JDK 7)

  

  - Serial GC - mark-sweep-compact 알고리즘 사용 (코어 수가 적을때 적합)
    1. 살아있는 객체를 식별
    2. 힙의 앞 부분부터 확인하여 살아있는것만 남긴다. 
    3. 살아남은 객체를 힙의 앞으로 쭉땡겨서 객체가 있는 부분과 없는 부분으로 나눈다.
  - Parallel GC - Serial GC랑 방식은 같으나 GC 쓰레드가 여러개이다. (코어가 많을때 유리)
  - Parallel Old GC - ?
  - Concurrent Mark & Sweep GC
    1. **클래스 로더**에서 가까운 객체 중 살아있는 것을 찾는다. 
  - G1(Garbage First) GC

  

  ### Serial GC

  

  - 사용하지 않는 참조들을 null로 바꾸면 GC는 반환해도 좋은 객체인지 판별할 수 있게 된다.

- Background thread