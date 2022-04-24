---
title: Spring RequestMapping 3편
author: self
date: 2022-04-24 18:15:00 +0900
categories: [Java, Spring]
tags: [java, spring, mapping]
---

> 해당 원글은 미디엄 블로그에서 작성한 내용을 해당 블로그에 기록하기 위해 가져옴.
> 원글은 2021.06.19일 작성.


## 개요
* 3편까지 쓸 내용은 아닌데 이거 저거 부가적으로 추가하다 보니 3편까지 왔다.
* 3편은 Response이다. 내용이 길…짧..? 모르겠다 길어지면 4편까지 이어질지도…

## Response
* 우선 legacy Controller와 RestController의 차이점은 ResponseBody가 포함여부이다. Controller + ResponseBody = RestController
* 그래서 Controller에 ResponseBody가 붙이지 않고 그냥 string을 리턴하면 ViewResolver 설정에 의해 string에 해당하는 jsp page로 연결된다.

``` java
@Controller
@RequestMapping("/req")
public class RequestApiController {
    @RequestMapping(value = "/home")
    public String index() {
        return "index";
    }
}
// application.properties
spring.mvc.view.prefix=/WEB-INF/view/
spring.mvc.view.suffix=.jsp
```

* Spring Boot ViewResolver 기본 of 기본 설정 참고한 [사이트](https://howtodoinjava.com/spring-boot/spring-boot-jsp-view-example/)

## Spring 예시
* Spring Boot라서 정말 간단하다. 참고한 사이트에선 ViewResolver 설정을 Configuration Annotation을 이용하여 설정하는 법에 대한 예시가 있는데 내가 설정한 Spring Boot 버전에선 implements 인터페이스 클래스가 deprecated된 클래스로 확인된다. 아래와 같이 설정 가능하다.

``` java
@Configuration
@EnableWebMvc
public class MvcConfiguration implements WebMvcConfigurer {

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/view/", ".jsp");
    }
}
```

* 위 application.properties 설정이 더 간단하지만 필요에 따라 골라서 사용하면 될 것 같다. ViewResolver는 여기까지만 확인하자.
* 그래서 RestController에서 String을 return하면 그대로 문자열이 표시된다.

> 한가지 실험..?

![](https://miro.medium.com/max/1400/1*5ZoWr55LbMKOOZrL8pgj8g.png)

JSP 페이지 내용 그대로

![](https://miro.medium.com/max/1134/1*cY3978DbdYQv9CxagQsUXA.png)

어느정도 표현은 된다.

> 옛날에는 이런방식으로도 개발을 했었다. 요즘엔 같이 일하는 동료를 부르고 싶다면 이렇게 작성한 뒤 commit 하면 된다.

## Response Json
* 다음은 가장 대중적인 json 방식으로 return해보자.
* json 방식으로 return 하려면 Key:Value 방식으로 VO를 return 해야한다.

> 간단하게 VO를 작성해보자. (아아 간단하지 않아…)

``` java
public class JsonResult {
    @Getter
    private String val1;
    @Getter
    private String val2;

    public JsonResult(String val1, String val2) {
        this.val1 = val1;
        this.val2 = val2;
    }

    @Override
    public int hashCode() {
        int val1Hash = val1 != null ? val1.hashCode() : 0;
        int val2Hash = val2 != null ? val2.hashCode() : 0;
        return val1Hash + val2Hash;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        JsonResult that = (JsonResult) o;

        if (val1 == null || val2 == null) {
            return false;
        }

        return ((JsonResult) o).getVal1().equals(this.getVal1()) && ((JsonResult) o).getVal2().equals(this.getVal2());
    }
}
```

* RestController에서 만든 VO를 return하도록 해보자

``` java
@GetMapping("/cash")
public JsonResult getCash(String num) {
    System.out.println("num : " + num);
    return new JsonResult("val1", num);
}
```

* Spring에서 MessageConverters가 자동으로 content-type에 따라 지정되어 response혹은 request를 처리한다.

* produces 기본이 Json이므로 잘 동작하는 것을 확인 할 수 있다.

## 마무리
* json을 가장 많이 쓰므로 여기까지만 알아보도록 하자. 4편이 나올수도 있고 아닐 수도 있는데 관련있으면 4편으로 하고 아니면 새로운 제목으로 다시 시작할 수 있다.

> 결론은…? Response를 전달할때 어떠한 content-type으로 전달할지 잘 생각하고 그에 맞게 Convert되도록 해줘야 한다는 걸 잊지말자.
