---
title: Spring Controller Post RequestBody with gson
author: self
date: 2022-04-24 18:25:00 +0900
categories: [Java, Spring]
tags: [java, spring, requestBody, json]
---

> 해당 원글은 미디엄 블로그에서 작성한 내용을 해당 블로그에 기록하기 위해 가져옴.
> 원글은 2021.06.25일 작성.


## RequestBody Json
* Spring Controller사용시 RequestBody를 통해 요청을 Json 타입(MediaType.APPLICATION_JSON_VALUE)으로 많이 사용한다. Json 이므로 key:value 형태로 값이 전달 될것이다.

* 하지만 RequestBody를 받는 것이므로 단순 String도 전달 가능하다. 아래 예시를 보자.

``` java
@RestController
public class RestTestController {
    @PostMapping(value = "post", consumes = MediaType.APPLICATION_JSON_VALUE)
    public String postRest(@RequestBody String text) {
        System.out.println("text : " + text);
        return "receive text : " + text;
    }
}
```

* 간단하게 문자열만 받아서 처리할 수 있는 형태이다. 기본적으로 Spring Boot는 메세지 컨버터를 이용해 RequestBody를 처리한다. Json default 메세지 컨버터는 MappingJackson2HttpMessageConverter이다.(2.5.1 기준)

## MessageConverter
* 기본으로 사용해도 좋지만 커스텀 메세지 컨버터를 사용하는 경우가 있을 수 있다. 그전에 GsonMessageConverter로 한번 바꿔보자.
* 방법을 참고한 [사이트](https://kok202.tistory.com/131)

``` properties
spring.http.converters.preferred-json-mapper=gson
```

* 이런방식으로 바꿔도 아무런 문제가 없다. gson이든 jackson이든 원하는 대로 동작한다.
* 이번엔 커스텀 gsonMessageConverter를 만들어보자.

---
* Config Class 작성하고

``` java
@Configuration
public class ApplicationConfig implements WebMvcConfigurer {

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {

        List<HttpMessageConverter<?>> result = new ArrayList<>(converters.size() + 2);
        Gson gson = new GsonBuilder().create();

        System.out.println("inject custom converter");

        CustomGsonHttpMessageConverter gsonMessageConverter = new CustomGsonHttpMessageConverter();
        gsonMessageConverter.setGson(gson);
        result.add(gsonMessageConverter);

        converters.clear();
        converters.addAll(result);

    }
}
```

* CustomGsonHttpMessageConverter 작성하고, (GsonHttpMessageConverter 클래스 그대로 모방)

``` java
public class CustomGsonHttpMessageConverter extends AbstractJsonHttpMessageConverter {
    private Gson gson;

    public CustomGsonHttpMessageConverter() {
        this.gson = new Gson();
    }

    public CustomGsonHttpMessageConverter(Gson gson) {
        Assert.notNull(gson, "A Gson instance is required");
        this.gson = gson;
    }

    public void setGson(Gson gson) {
        Assert.notNull(gson, "A Gson instance is required");
        this.gson = gson;
    }

    public Gson getGson() {
        return this.gson;
    }

    protected Object readInternal(Type resolvedType, Reader reader) throws Exception {
        System.out.println("이곳에서 요청을 처리.");
        return this.getGson().fromJson(reader, resolvedType);
    }

    protected void writeInternal(Object object, @Nullable Type type, Writer writer) throws Exception {
        System.out.println("이곳에서 응답을 처리.");
        if (type instanceof ParameterizedType) {
            this.getGson().toJson(object, type, writer);
        } else {
            this.getGson().toJson(object, writer);
        }

    }
}
```

* 커스텀 컨버터는 Gson gson = new GsonBuilder().create(); 이렇게 Gson Library를 그대로 사용한다.
* 그리고 한번 테스트 해보자.

> 두둥!! 아래와 같은 오류가 발생한다.

```
com.google.gson.stream.MalformedJsonException: Use JsonReader.setLenient(true) to accept malformed JSON at line 1 column 7 path $
```

* 순수 gson library는 단순 String 형태를 convert할 때 문자열에 띄어쓰기가 존재하면 해당 오류를 내뱉는다.

![](https://miro.medium.com/max/1220/1*w1h17PtzGoy0jgQYDRpYww.png)

이렇게 띄어쓰기는 제대로 컨버트 되지 않는다.

* 아래와 같이 하나의 String으로 묶어주면 원하는대로 동작 가능

![](https://miro.medium.com/max/1400/1*1ceTeIdakU7Lli7CybgCEA.png)

신기하게도 응답도 묶여서 옴

![](https://miro.medium.com/max/1400/1*xJVxS0WjQbUJgPXmyyUbSA.png)

아까 기본 Jasckson 일때 비교짤

* 하나의 String으로 묶어서 전달하는 방법. Script를 예시로 들면 ‘“test text”’이렇게 전달.

> 아니면 key:value로 전달.

## 마무리
* 커스텀 메세지 컨버터에 기본 gson library를 이용하는 경우에 발생할 수 있는 이슈에 대해 알아보았다. 이렇게 사용하는 일이 드물지만 만약 사용하는 경우 유의해서 사용해야한다.
