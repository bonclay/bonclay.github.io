---
title: JAXBContext, Unmarshaller와 lombok
author: self
date: 2022-04-23 19:30:00 +0900
categories: [Java]
tags: [java, JAXBContext, Unmarshaller, lombok]
---

> 해당 원글은 미디엄 블로그에서 작성한 내용을 해당 블로그에 기록하기 위해 가져옴.
> 원글은 2021.01.22일 작성.

## 개요

* 작업하다 보면 가끔씩 xml을 parsing해야 할 때가 종종 있다. json이면 참 편했을텐데 현실은 그렇지 않지… xml을 parsing하기 위해 어떤 libarary를 사용하면 좋을까~ 하고 검색해보면 꽤 다양한 library가 존재한다. gson-xml이라는 gson에서 만든 library도 있고, JDK 에서 제공하는 XPath, JAXBContext도 있고 다양한데 무엇을 선택하여 사용할지는 개발자의 마음이다.
* 여기선 어떤게 좋을까?라기보다 JAXBContext 사용하다가 발견한 사소한 정보를 공유하고자 한다.


> 우선 기본적인 JAXBContext 사용법은 검색하면 쭈르륵 나오니까 패스. 우리(?)가 좋아하는 lombok과 같이 한번 사용해보자.
{: .prompt-info }

## 사용 예시

### xml정보를 담을 간단한 class

``` java
@Getter
@XmlRootElement
public class User {
   @XmlElement
   private String name;
   @XmlElement
   private String character;
}
```

### 그리고 본격적으로 JABContext를 사용하여 xml을 parsing

``` java
@Test
public void unmarshallerTest() throws Exception {
   String response = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"
         + "<user>\n"
         + "    <name>토끼겅쥬</name>\n"
         + "    <character>전사</character>\n"
         + "</user>";
   JAXBContext jaxbContext = JAXBContext._newInstance_(User.class);
   Unmarshaller unmars = jaxbContext.createUnmarshaller();
   User user = (User) unmar.unmarshal(new StringReader(response));

   System.out.println( "name : " + user.getName());
   System.out.println( "character : " + user.getCharacter());
}
```

### 결과는 기대했던대로 잘 나온다.

```
name : 토끼겅쥬
character : 전사
```

### 여기서 User class에 임의의 새로운 값이 필요하여 Getter를 Data annotation으로 바꾸고 다시 실행해보자.

``` java
@Data
@XmlRootElement
public class User {
   @XmlElement
   private String name;
   @XmlElement
   private String character;

   private int level;
}
```

### 결과는 runtime Exception이 발생해버렸다.

```
com.sun.xml.bind.v2.runtime.IllegalAnnotationsException: 2 counts of IllegalAnnotationExceptions클래스에 동일한 이름 "character"을(를) 사용하는 속성이 두 개 있습니다.
 this problem is related to the following location:
  at public java.lang.String com.naver.land.vapi.User.getCharacter()
  at com.naver.land.vapi.User
 this problem is related to the following location:
  at private java.lang.String com.naver.land.vapi.User.character
  at com.naver.land.vapi.User
클래스에 동일한 이름 "name"을(를) 사용하는 속성이 두 개 있습니다.
 this problem is related to the following location:
  at public java.lang.String com.naver.land.vapi.User.getName()
  at com.naver.land.vapi.User
 this problem is related to the following location:
  at private java.lang.String com.naver.land.vapi.User.name
  at com.naver.land.vapi.User
```

* 이유가 뭘까나? JAXBContext, Annotation, Lombok 사용의 콜라보로 인한 오류인데 JAXBContext 동작 방식을 살펴보자

* 우선 @XmlElement annotation에 주목할 필요가 있다. unmarshal을 할때 해당 annotation이 지정 된 곳에 값이 세팅이 되는데 위의 예제엔 필드값에 지정이 되어 있기 때문에 필드값으로 바로 값이 세팅이 된다.
그래서 setter를 사용하고 싶으면 setter 메소드에 annotation을 지정하면 된다.

### 이런식으로 setter에 @XmlElement를 추가

``` java
@Getter
@XmlRootElement
public class User {
   private String name;
   private String character;

   @XmlElement
   public void setName(String name) {
      this.name = name;
   }

   @XmlElement
   public void setCharacter(String character) {
      this.character = character;
   }
}
```

* 자 이쯤 되면 Data lombok annotaion을 사용했을 때 왜 아까와 같은 Exception이 발생했는지 파악했을 것이다. Data를 사용했다면 아래와 같이 풀어서 Getter,Setter가 각 필드에 지정되었을 것이다. XmlElement이 Setter에도 지정이 되어 필드,Setter 두곳에 동일한 이름의 속성이 생성되어 버린것이다.

``` java
@XmlRootElement
public class User {
   @Getter
   @Setter
   @XmlElement
   private String name;
   @Getter
   @Setter
   @XmlElement
   private String character;
}
```

## 마무리
* 그러므로 JABContext를 사용할 때 lombok을 같이 사용하고 싶다면 필드별로 Getter, Setter를 지정하는 것이 좋다.
* 넘 소박하고 사소한 정보였지만, 나 같이 오랜만에 xml parsing하려는 분이 있다면 도움이 되었길 바란다. ㅠ
* 참고로 검색하다가 봤는데 JABContext는 한번만 생성되는 게 좋다고 한다. 리소스가 생각보다 많이 소모 된다고 함. 반복문 안보다는 바깥에 놓도록 하자.
