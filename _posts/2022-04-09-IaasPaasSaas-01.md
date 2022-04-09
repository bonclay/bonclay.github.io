---
title: IaaS, PaaS, SaaS
author: self
date: 2022-04-09 18:50:00 +0900
categories: [cloud]
tags: [cloud]
---

## 개요
Cloud 서비스를 이해하기 위해 IaaS, PaaS, SaaS 개념에 대해 이해해 보자.

## Cloud 서비스 모델
![클라우드 서비스 모델 비교](https://raw.githubusercontent.com/bonclay/bonclay.github.io/main/image/blog_0409_01.png)
- 출처 : https://www.whatap.io/ko/blog/9/

## IaaS
- Infrastructure as a service

### 설명
> 물리적 컴퓨팅 리소스, 위치, 데이터 분할, 확장, 보안, 백업 등과 같은 기본 네트워크 인프라의 다양한 하위 수준 세부 정보를 역참조하는 데 사용되는 상위 수준 API를 제공하는 온라인 서비스입니다. Xen, Oracle VirtualBox, Oracle VM, KVM, VMware ESX/ESXi 또는 Hyper-V는 가상 머신을 게스트로 실행합니다. 클라우드 운영 체제 내의 하이퍼바이저 풀은 많은 수의 가상 머신과 고객의 다양한 요구 사항에 따라 서비스를 확장 및 축소할 수 있는 기능을 지원할 수 있습니다.
{: .prompt-info }

## PaaS
- Platform as a Service

### 설명
> PaaS(Platform as a Service) 또는 aPaaS(Application Platform as a Service) 또는 플랫폼 기반 서비스는 고객이 컴퓨팅 플랫폼과 하나 또는 일반적으로 애플리케이션 개발 및 실행과 관련된 인프라 구축 및 유지 관리의 복잡성 없이 더 많은 애플리케이션, 개발자가 그러한 소프트웨어 번들을 생성, 개발 및 패키징할 수 있도록 합니다.
{: .prompt-info }

## SaaS
- Software as a service

### 설명
> 서비스로서의 소프트웨어(SaaS)는 소프트웨어 라이선스 및 제공 모델로, 구독 기반으로 라이선스가 부여되고 중앙에서 호스팅됩니다. SaaS는 "주문형 소프트웨어" 및 웹 기반/웹 호스팅 소프트웨어로도 알려져 있습니다.
SaaS는 IaaS(Infrastructure as a Service), PaaS(Platform as a Service), DaaS(Desktop as a Service), MSaaS(Managed Software as a Service), 모바일 백엔드와 함께 클라우드 컴퓨팅의 일부로 간주됩니다. 서비스로서의 데이터 센터(MBaaS), 서비스로서의 데이터 센터(DCaaS), 서비스로서의 통합 플랫폼(iPaaS) 및 서비스로서의 정보 기술 관리(ITMaaS).
SaaS 앱은 일반적으로 씬 클라이언트를 사용하여 사용자가 액세스합니다. 웹 브라우저를 통해. SaaS는 사무용 소프트웨어, 메시징 소프트웨어, 급여 처리 소프트웨어, DBMS 소프트웨어, 관리 소프트웨어, CAD 소프트웨어, 개발 소프트웨어, 게임화, 가상화, 회계, 협업, 고객 관계 관리(CRM)를 포함한 많은 비즈니스 애플리케이션의 공통 제공 모델이 되었습니다.
{: .prompt-info }

## 마무리
- 개념 자체는 어렵지 않은 것 같은데 실제 플랫폼을 제공하려 할때 뭔가 구분이 모호한 경우도 생기는 것 같다.
