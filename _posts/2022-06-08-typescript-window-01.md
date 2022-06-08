---
title: TypeScript Global Variables. feat! window
author: self
date: 2022-06-08 23:30:00 +0900
categories: [TypeScript, Kubeflow pipeline]
tags: [kubeflow pipeline, react, typescript, window]
---

## 개요
* Kubeflow pipelines frontend 코드 살펴보다가 TypeScript 관련된 내용 정리.

## Why
* pipeline frontend side navigation 숨겨볼라고 코드 살펴보다가 확인.
* window['KFP_FLAGS'] -> 이거 뭐???

```typescript
export const KFP_FLAGS = {
  DEPLOYMENT:
    // tslint:disable-next-line:no-string-literal
    window && window['KFP_FLAGS']
      ? // tslint:disable-next-line:no-string-literal
        window['KFP_FLAGS']['DEPLOYMENT'] === Deployments.KUBEFLOW
        ? Deployments.KUBEFLOW
        : // tslint:disable-next-line:no-string-literal
        window['KFP_FLAGS']['DEPLOYMENT'] === Deployments.MARKETPLACE
        ? Deployments.MARKETPLACE
        : DEPLOYMENT_DEFAULT
      : DEPLOYMENT_DEFAULT,
  HIDE_SIDENAV: window && window['KFP_FLAGS'] ? window['KFP_FLAGS']['HIDE_SIDENAV'] : false,
};
```


## HIDE_SIDENAV
* window.KFP_FLAGS.HIDE_SIDENAV 값으로 연결됨.
* 쓰임새

![SideNav.tsx](https://raw.githubusercontent.com/bonclay/bonclay.github.io/main/image/blog_0608_01.png)

> true이면 아래 sideNav를 그리지 않고 null을 return


## 값 지정
* window.KFP_FLAGS.HIDE_SIDENAV=true;
  * window는 해당 page의 global 값을 저장하는 dom 객체로 사용됨.
  * script로 위와 같이 값을 지정하면 아래와 같은 예시로 호출

```typescript
window['KFP_FLAGS']['HIDE_SIDENAV']
```

> 위의 예시에도 나와있다.

* pipeline frontend에선 index.html에서 값을 지정할 수 있다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    <link rel="shortcut icon" href="%PUBLIC_URL%/static/favicon.ico">
    <title>Kubeflow Pipelines</title>
    <script>
      window.KFP_FLAGS={};
      window.KFP_FLAGS.DEPLOYMENT='KUBEFLOW';
      window.KFP_FLAGS.HIDE_SIDENAV=true;
    </script>
    <script id="kubeflow-client-placeholder"></script>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
  </body>
</html>
```

## 마무리
> window라고 달랑 지정되어 있고 값을 어디서 지정하는지 몰랐는데 알고보니 dom 객체...
> 참고로 HIDE_SIDENAV를 true로 지정하면 아래와 같이 sidebar 사라짐.

![Frontend Main](https://raw.githubusercontent.com/bonclay/bonclay.github.io/main/image/blog_0608_02.png)

