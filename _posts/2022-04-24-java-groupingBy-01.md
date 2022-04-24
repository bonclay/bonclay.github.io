---
title: Java Stream groupingBy
author: self
date: 2022-04-24 17:55:00 +0900
categories: [Java]
tags: [java, stream, groupingBy]
---

> 해당 원글은 미디엄 블로그에서 작성한 내용을 해당 블로그에 기록하기 위해 가져옴.
> 원글은 2021.05.28일 작성.


> 지난 달 글을 4월에 올린 것 같은데 너무 글을 안올린 것 같아서 급하게 하나 작성한다.

## 개요
* 그간 이직 준비를 하느라 코테를 연습했는데 하다보니 사용할 만한 Stream 기능이 있어서 기록해 두려고 한다. 그거슨 바로바로 groupingBy!

## groupingBy
* API 문서에는 아래와 같이 나와있다.

![java.util.stream.Collectors](https://miro.medium.com/max/1400/1*rOKfTfvMASATPytDsj__5w.png)

([https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html))

* 일단은 간단하게 알아보자.
* _T 유형의 입력 요소에 대해 “group by ”작업을 구현하고 분류 함수에 따라 요소를 그룹화하고 결과를 맵에 반환하는 Collector를 반환합니다._

> 무슨말인고~?

## 예제
* 예제를 통해 아라보자.
* 아래와 같은 Integer List가 있다. 각 숫자별로 그룹을 지어서 Map형으로 변환 하고 싶다. 1={1}, 2={2,2} 이런식으로

``` java
List<Integer> testList = Arrays.asList(1,2,3,4,5,6,7,5,3,2,5,5,7,8,5,4,3,3,4,6,7);
```

* Stream groupingBy를 이용하면 아주 쉽게 만들 수 있다.

``` java
Map<Integer, List<Integer>> testMap = testList.stream()
        .collect(Collectors.groupingBy(i -> i));
// testMap : {1=[1], 2=[2, 2], 3=[3, 3, 3, 3], 4=[4, 4, 4], 5=[5, 5, 5, 5, 5], 6=[6, 6], 7=[7, 7, 7], 8=[8]}
```

> 엄청 쉽죠잉~?
* 이상 코테에서나 쓸법한 예제였다.

## 그룹화
* 다음은 Class List를 그룹화 해보자, 그나마 실무에 적용할만하다.
* 예를 들어 쉽게 아래와 같은 class를 정의하자

``` java
static class Student {
    String name;
    String subject;
    public Student(String name, String subject) {
        this.name = name;
        this.subject = subject;
    }
}
List<Student> students = new ArrayList<>();
students.add(new Student("강", "수학"));
students.add(new Student("강", "과학"));
students.add(new Student("강", "영어"));
students.add(new Student("강", "국어"));
students.add(new Student("김", "수학"));
students.add(new Student("김", "과학"));
students.add(new Student("박", "언어"));
students.add(new Student("박", "경제"));
students.add(new Student("박", "토익"));
```

* 학생이 듣는 과목을 묶어서 Map으로 만들어보자

``` java
Map<String, List<Student>> studentSub = students.stream()
        .collect(Collectors.groupingBy(Student::getName));
// {김=[김 : 수학, 김 : 과학], 박=[박 : 언어, 박 : 경제, 박 : 토익], 강=[강 : 수학, 강 : 과학, 강 : 영어, 강 : 국어]}
```

* 여기서 value의 Student를 subject로 바꾸는 방법을 찾고 있는데 고민을 좀 해보고 있다.

```
Map<String, List<String>> results = studentSub.entrySet().stream().collect(Collectors.toMap(
        e -> e.getKey(),
        e -> e.getValue().stream()
                .map(Student::getSubject)
                .collect(Collectors.toList())
));
```

* 이런식으로 할 수 있으나 가독성도 좋지 않고 좀 길다.

## 마무리
어쨋든 코테에서 사용해 볼 수 있는 groupingBy에 대해 간단히 알아봤다. 심화 과정은 시간이 좀 나면 작성해보자.
