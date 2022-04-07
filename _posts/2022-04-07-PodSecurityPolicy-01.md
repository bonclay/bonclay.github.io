---
title: PodSecurityPolicy unable to admit pod
author: self
date: 2022-04-07 22:50:00 +0900
categories: [k8s, kubeflow, pipeline]
tags: [k8s, kubeflow, pipeline, pod, PodSecurityPolicy]
---

## 개요
k8s에 kubeflow를 설치 한 뒤 pipeline을 Run 하려고 할때 PodSecurityPolicy: unable to admit pod 오류 발생.

## 오류 내용
### 메세지 예시
```
This step is in Error state with this message: task 'xx' errored: pods "kube-pipeline" is forbidden: PodSecurityPolicy: unable to admit pod: []
```

### 발생 환경
- cluster plugin PodSecurityPolicy가 enable상태일 경우. kubeflow 1.5 환경에서 발생.

### 원인
- 참고 : https://kubernetes.io/docs/concepts/security/pod-security-policy/
- 대략적인 요약
  - Pod를 생성할 때 일반적으로 컨트롤러 관리자를 통해 생성. 정책에 대한 컨트롤러 액세스 권한이 필요한데 해당 정책이 없으면 Pod생성이 되지 않음.

## PodSecurityPolicy
일단 k8s 1.25에서 삭제될 예정이다.

> Caution: PodSecurityPolicy is deprecated as of Kubernetes v1.21, and will be removed in v1.25.

## 해결 방법
- kubeflow는 rbac을 사용.
- 아래와 같이 ClusterRole의 rules에 정책 추가

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: <role name>
rules:
- apiGroups: ['policy']
  resources: ['podsecuritypolicies']
  verbs:     ['use']
  resourceNames:
  - <list of policies to authorize>
```

### kubeflow ClusterRole 대상
- kubeflow-kubernetes-admin
- kubeflow-kubernetes-edit
- kubeflow-kubernetes-view

대상 ClusterRole 모두 수정 후 pipeline을 run해보자.

### 마치며

> 실제 테스트는 생략한다. kubeflow에서 사용하는 ClusterRole이 많아 PSP를 사용하는 Cluster의 경우엔 여러가지 확인이 필요하다.
{: .prompt-tip }
