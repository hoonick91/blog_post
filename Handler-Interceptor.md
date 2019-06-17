---
title: Handler Interceptor
typora-copy-images-to: Handler Interceptor
date: 2019-05-16 08:18:33
tags:
categories: spring framework
---

HandlerMapping에서 interceptor를 추가해준다.

요청 처리 전/후에 무언가 처리하고 싶으면 interceptor를 추가한다.

1. preHandle 1
2. preHandle 2
3. 요청처리
4. postHandle 2
5. postHandle 1 - 다음과 같이 역순으로 호출 된다.
6. 뷰 렌더링
7. afterCompletion 2
8. afterCompletion 1



ServletFilter와의 차이점

- 더 구체적이다.
- 일반적인 기능을 구현할때 - ServletFilter
- Spring에 특화된 정보를 참고해야 한다. - HandlerInterceptor
- Xss attack
  - 웹의 form에 다가 script를 넣어 해킹하는 방법 - ServletFilter
  - Naver - LUCY XSS



HandlerInterceptor을 구현하기 위해서는 Interceptor java파일을 생성해야한다.

