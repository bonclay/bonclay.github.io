---
title: Java Bean Lifecycle
author: self
date: 2022-05-08 21:30:00 +0900
categories: [Java, Bean]
tags: [java, bean, lifecycle]
---

## 개요
스프링 빈 라이프사이클 관리를 통해 특점 시점에 통지를 받을 수 있음.

## 빈 라이프사이클
![BeanLifecycle](https://raw.githubusercontent.com/bonclay/bonclay.github.io/main/image/blog_0508_01.png)
* 출처 : (책) 전문가를 위한 스프링 5

## 생성 통지 메커니즘
* 메서드 메커니즘
* annotation 메커니즘
* 인터페이스 메커니즘

> 라이프사이클 통지를 받을 때 어떤 메커니즘을 사용할지는 애플리케이션 요구사항에 달려있습니다.

### 메서드 메커니즘
* init 메서드 생성
```java
public class Singer {
  ...
    private void init() {
        System.out.println("빈 초기화");
      ...
    }
  ...
}
```
* init-method="init" Context 구성
```xml
<bean id="singerOne"
    class="com.apress.prospring5.ch4.Singer"
    init-method="init" p:name="John Mayer" p:age="39"/>
```

### InitializingBean 인터페이스 구현
* afterPropertiesSet()
```java
public class SingerWithInterface implements InitializingBean {
  ...
    public void afterPropertiesSet() throws Exception {
        System.out.println("빈 초기화");
      ...
    }
  ...
}
```

* 일반적인 Context 구성
```xml
<bean id="singerOne"
    class="com.apress.prospring5.ch4.SingerWithInterface"
    p:name="John Mayer" p:age="39"/>
```

### JSR-250 @PostConstruct 사용
* @PostConstruct
```java
public class SingerWithJSR250 {
  ...
    @PostConstruct
    private void init() throws Exception {
        System.out.println("빈 초기화");
      ...
    }
  ...
}
```

* 일반적인 Context 구성
```xml
<context:annotation-config/>
```

### @Bean 초기화 메서드 선언
* @Bean 지정시 initMethod를 지정할 수 있음
* 예시
```java
public class SingerConfigDemo {
    @Configuratioin
    static class SingerConfig {
      @Lazy
      @Bean(initMethod = "init")
      Singer singleOne() {
        Singer singerOne = new Singer();
        singerOne.setName("John Mayer");
        singerOne.setAge(39);
        return singerOne;
      }
    }
}
```

## 초기화 메서드 해석 순서 이해하기
1. 빈 생성을 위해 생성자를 호출
2. 의존성을 주입
3. 사전 초기화 담당 BeanPostProcessor 기반 빈들에게 확인 요청. @PostConstruct annotation은 CommonAnnotationBeanPostProcessor 빈이 적용된 메서드 호출. afterPropertiesSet, init-method 이전에 수행
4. InitializingBean의 afterPropertiesSet 실행
5. init-method 초기화 메서드 맨 마지막에 실행

## 소멸 통지
* 일반적으로 소멸 콜백은 초기화 콜백과 쌍으로 사용.

### 메서드
* init-method="init" Context 구성
```xml
<bean id="destructiveBean"
    class="com.apress.prospring5.ch4.DestructiveBean"
    destroy-method="destroy"
    init-method="afterPropertiesSet"
    p:filePath=""
/>
```

### DisposableBean 인터페이스 구현
```java
public class DestructiveBeanWithInterface implements DisposableBean {
  ...
    @Override
    public void destroy() {
        System.out.println("빈을 소멸합니다.");
      ...
    }
  ...
}
```

### JSR-250 @PreDestroy 사용
```java
public class DestructiveBeanWithJSR250 {
    @PreDestroy
    public void destroy() {
        System.out.println("빈을 소멸합니다.");
    }
}
```

### @Bean 사용 소멸 메서드 정의
```java
public class DestructiveBeanConfigDemo {
    @Configuration
    static class DestructiveBeanConfig {
        @Lazy
        @Bean(destroyMethod = "destroy")
        DestructiveBean destructiveBean() {
            DestructiveBean destructiveBean = new DestructiveBean();
            destructiveBean.setFilePath("");
            return destructiveBean;
        }
    }
}
```
### 해석 순서
> 초기화와 동일

## 마무리
> 가끔 빈 초기화시 작업이 필요할 때 한번씩 찾아보면 좋을 듯 하다. 상황에 따라 골라 사용. 셧다운 후크는 패스하겠다.
