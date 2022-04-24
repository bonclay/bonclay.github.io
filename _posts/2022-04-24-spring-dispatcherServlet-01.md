---
title: Spring DispatcherServlet
author: self
date: 2022-04-24 18:30:00 +0900
categories: [Java, Spring]
tags: [java, spring, dispatcherServlet]
---

> 해당 원글은 미디엄 블로그에서 작성한 내용을 해당 블로그에 기록하기 위해 가져옴.
> 원글은 2021.06.30일 작성.


## 개요
* 이번 주제는 DispatcherServlet이다. 흔히들 Front Controller라고도 부르는데 아무래도 Controller 앞단에서 Controller를 호출하는 역할을 해서 그런 것 같다.

## MVC
* 아래는 기본적인 MVC 패턴의 흐름이다.

![](https://miro.medium.com/max/1400/0*qmaU6vkJt3IKGPua.png)

출처:[https://stackoverflow.com/questions/2769467/what-is-dispatcher-servlet-in-spring](https://stackoverflow.com/questions/2769467/what-is-dispatcher-servlet-in-spring)

1.  요청을 DispatcherServlet에서 받아서
2.  Handler mapping으로 Controller 호출
3.  Controller에선 응답을 수행
4.  결과를 Model and logical view name으로 전달
5.  ViewResolver는 view name을 확인하여 View를 전달
6.  생성된 View를 Response로 전달

## DispatcherServlet
* DispatcherServlet은 간단하게 아래와 같이 동작한다.

``` java
public class SimpleDispatcher extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse res) {
        // do get
        process(req.getRequestURI());
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse res) {
        // do post
        process(req.getRequestURI());
    }

    public void process(String uri) {
        HandlerMapping handlerMapping = new HandlerMapping();
        handlerMapping.init();

        Controller controller = handlerMapping.getController(uri);
        controller.request();
    }
}
public class HandlerMapping {
    Map<String, Controller> controllerMap = new HashMap<>();

    public void init() {
        controllerMap.put("/login", new LoginController());
    }

    public Controller getController(String uri) {
        return controllerMap.get(uri);
    }
}
public class LoginController implements Controller{
    @Override
    public void request() {
        // Do Something
    }
}
```

* 간단하게 만들어 봤는데 실제와 비슷하지 않겠지만 대충 이런 흐름으로 동작한다고 보면된다.

> Spring에선 이러한 작업들을 딱히 신경 쓰지 않아도 제공해 주니 정말 편하게 구현할 수 있다.

## 마무리
어쨋든 DispatcherServlet도 Servlet이니 기본적인 동작은 Servlet으로 동작한다는 것을 알아두고 넘어가면 될 것 같다. 참고로 Spring DispatcherServlet은 Servlet관련 여러 추상클래스를 단계별로 상속받고 있다.
