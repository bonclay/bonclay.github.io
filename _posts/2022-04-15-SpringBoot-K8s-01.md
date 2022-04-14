---
title: SpringBoot 프로젝트를 kubernetes 환경에 배포
author: self
date: 2022-04-15 00:10:00 +0900
categories: [k8s, spring boot]
tags: [k8s, spring boot, minikube, deployment, nodeport]
---

## 개요
SpringBoot 프로젝트를 kubernetes 환경에 배포하여 접속까지 확인하는 테스트를 진행한다.

## SpringBoot 프로젝트 생성

간단하게 [Spring Initializer](https://start.spring.io/)를 통해서 생성한다.

### RestController 설정
배포 후 확인을 위해 간단하게 설정.

```java
@RestController("/")
public class SampleRestController {
    @Value("${appName}")
    private String appName;

    @GetMapping
    public String indexHome() {
        return appName + " index";
    }
}
```

## 배포 환경 구성
 - `kubernetes` 환경을 로컬에서 테스트 하기 위해 `minikube`를 이용할 것이다.
 - 또한 배포를 위해 `docker hub`에 image를 push 해야한다.
 - minikube 및 docker 설치(pass 함)는 다른 [Post](https://bonclay.github.io/posts/minikube-01/)에서 다루었다.

## 빌드 및 배포
---
### SpringBoot 빌드

```console
$ ./gradlew bootJar
```

혹은 Intellij를 이용한 Gradle 빌드

![Gradle 빌드](https://raw.githubusercontent.com/bonclay/bonclay.github.io/main/image/blog_0415_01.png)

jar 파일은 프로젝트홈/build/lib/ 디렉토리 밑에 생성된다.

### Docker Build & Push
 - Dockerfile 간단하게

```Dockerfile
FROM adoptopenjdk/openjdk11
ENV APP_HOME=/usr/app/
WORKDIR $APP_HOME
COPY ./*.jar ./app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

 - build 및 push
   - build는 자신의 docker hub repository 밑으로 지정한다.
   - Dockerfile이 존재하는 directory에서 아래 커맨드 수행

```console
docker build -t {repositoryName}/{appName}:{tag} .
docker push {repositoryName}/{appName}:{tag}
```

> 이제 kubernetes 환경에 배포할 준비가 다 되었다.

### minikube에 배포

deployment와 nodeport yaml 파일을 생성.

- deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: appName
  labels:
    app: appName
spec:
  replicas: 1
  selector:
    matchLabels:
      app: appName
  template:
    metadata:
      labels:
        app: appName
    spec:
      containers:
      - name: appName
        image: docker.io/repositoryName/appName:tag
        ports:
        - containerPort: 8080
```

- nodeport yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-service
spec:
  type: NodePort
  selector:
    app: appName
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30000
```

#### minikube 시작

기본 설정으로 그냥 start

```console
minikube start
```

설치 후 kubernetes 환경이 제대로 구성 되었는 지 확인

```console
kubectl get pods -A
```

![Minikube k8s 환경](https://raw.githubusercontent.com/bonclay/bonclay.github.io/main/image/blog_0415_02.png)

#### yaml 설치

```console
kubectl apply -f deployment.yaml
kubectl apply -f nodeport.yaml
```

#### 확인
> docker hub에 제대로 image가 push 되었다면 문제없이 minikube kubernetes 환경에 배포가 되었을 것이다.
{: .prompt-warning }

pod가 Running 상태로 변경되었다면 port-forwarding을 통해 minikube kubernetes 서비스에 접속해보자.

```console
kubectl port-forward service/nodeport-service 9090:80
```

> curl 혹은 브라우저를 통해 http://localhost:9090 으로 접속해보면 배포가 된 것을 확인할 수 있다.

## 마무리
- 해당 프로젝트를 통해 ConfigMap을 활용하는 것 까지 다음 포스트에서 다루어 볼 예정.
