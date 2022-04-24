---
title: Spring RequestMapping 2편
author: self
date: 2022-04-24 18:05:00 +0900
categories: [Java, Spring]
tags: [java, spring, mapping]
---

> 해당 원글은 미디엄 블로그에서 작성한 내용을 해당 블로그에 기록하기 위해 가져옴.
> 원글은 2021.06.14일 작성.

## 개요
* 2편은 Controller에 값을 전달 받는 방법 확인하기.

## Controller
> 4가지 annotation을 사용하는게 가장 일반적이다.

* RequestParam
* RequestBody
* ModelAttribute
* PathVariable

## RequestParam
* 우선 RequestParam 방식.
* 간단하게 값을 받을 때 유용하게 사용된다.

``` java
@GetMapping("/cash")
public String getCash(@RequestParam String num) {
    System.out.println("num : " + num);
    return "get";
}
//http://localhost:8080/cash?num=2
//num : 2
```

* annotation 생략도 가능하다.

``` java
@GetMapping("/cash")
public String getCash(String num) {
    System.out.println("num : " + num);
    return "get";
}
```

* Object로 값을 전달받는 방법이 있는데 annotation 없이 사용하면 어떻게 될까?
* 아래와 같이 Class를 하다 만들고 Get 방식으로 값을 받아보자.

``` java
public class CashParam {
    @Getter
    int num;
    @Getter
    String account;
}
...
@GetMapping("/cash")
public String getCash(CashParam param) {
    System.out.println("param : " + param.getNum());
    return "get";
}
```

* 아까와 같은 방식으로 (cash?num=2) 전달하면 원하는대로 동작하지 않는다 그 이유는 int num 변수에 Setter가 없기 때문이다. annotation을 생략하면 또 자동으로 ModelAttribute 방식으로 동작한다는 것을 알 수 있다.

``` java
public class CashParam {
    @Getter
    @Setter
    int num;
    @Getter
    String account;
}
```

## RequestBody
* 그렇다면 RequestBody로 지정을 해보자.

``` java
@GetMapping("/cash")
public String getCash(@RequestBody String num) {
    System.out.println("num : " + num);
    return "get";
}
```
![](https://miro.medium.com/max/1400/1*ny9kY4Px1nC332XWfFUhqA.png)

두둥!

* RequestBody는 말그대로 HttpRequest Body의 값을 받아오는 것이므로 Body가 없는 Get방식에는 사용할 수 없다.
* 다음은 PathVariable. 이렇게 간단하게 사용할 수 있다.

``` java
@GetMapping("/cash/{num}")
public String getCash(@PathVariable String num) {
    System.out.println("num : " + num);
    return "get";
}
```

## 마무리
* Controller에 값을 전달하는 다양한 방법을 알아 봤는데 하나의 메서드에 다양한 annotation을 적절히 섞어서 사용하면 된다.

참고한 블로그 — [https://mangkyu.tistory.com/72](https://mangkyu.tistory.com/72)
