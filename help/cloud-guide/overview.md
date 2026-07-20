---
title: 클라우드 인프라의 Commerce
description: 클라우드 인프라의 Commerce를 빌드하고, 배포하고, 관리하는 방법에 대해 알아봅니다.
exl-id: a37d0403-df14-4bb9-8cc4-25436560ba0c
TQID: https://experienceleague.adobe.com/-sgz85xapPKNipyFVB4yMrLilEku3ff5IJg3OddymsA
product_v2: id: eadea719-cf89-469b-a6fd-a236a7138047
feature_v2: id: ba9e5be9-7de1-4f71-a5d2-baead0e425eeid: dac87252-6066-4d6e-a9d2-f6d84c323de7id: e8818fe6-9c8b-4bc0-9ef8-377a10b7bc75
subfeature_v2: id: f8ddfd3b-6194-46e8-a176-0e918039be56
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bdid: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2: id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: bcac8986e748f6e513d4db22ee7eb7f64bac1a3a
workflow-type: tm+mt
source-wordcount: 323
ht-degree: 3%

---

# 클라우드 인프라의 Commerce

클라우드 인프라의 Adobe Commerce은 클라우드 기반 환경에서 [!DNL Commerce] 애플리케이션을 구축, 배포 및 관리하는 **셀프서비스** 접근 방식을 사용하는 자동화된 호스팅 플랫폼을 제공합니다. Adobe Commerce on cloud infrastructure에는 온-프레미스 Adobe Commerce 및 Magento Open Source 플랫폼과 구별되는 추가 기능이 포함되어 있습니다.

- PHP, MySQL(MariaDB), Redis, 메시지 큐 서비스([!DNL RabbitMQ] 또는 [!DNL ActiveMQ]) 및 지원되는 검색 엔진 기술을 포함하는 미리 제공된 인프라입니다.
- PaaS(Platform as a Service) 환경에서 코드 변경 사항을 푸시할 때마다 효율적인 신속한 개발 및 지속적인 배포를 위해 자동 빌드 및 배포가 포함된 Git 기반 워크플로우입니다.
- 사용자 지정이 용이한 환경 구성 파일 및 CLI(명령줄 인터페이스)를 통해 툴을 관리하고 구축할 수 있습니다.
- 온라인 판매 및 소매를 위한 확장 가능하고 안전한 환경을 제공하는 Amazon Web Services(AWS) 호스팅.

![클라우드의 이점](../assets/CloudBenefits.svg)

>[!NOTE]
>
>보안에 대한 자세한 내용은 [보안 시작 검사 목록](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/launch/checklist#security-configuration)을 참조하세요.

[기술 스택](architecture/tech-stack.md)을 자세히 보거나 [Commerce용 클라우드 아키텍처](architecture/cloud-architecture.md)의 특정 기능 및 지원 제품에 대해 자세히 알아보십시오.

<div id="recs-overview-body-1"></div>
<div id="recs-overview-body-2"></div>
<div id="recs-overview-body-3"></div>
<div id="recs-overview-body-4"></div>
<div id="recs-overview-body-5"></div>
<div id="recs-overview-body-6"></div>

## 클라우드 영역

다음 섹션에서는 클라우드 인프라에서 Adobe Commerce에 사용할 수 있는 다양한 AWS 및 Azure 지역에 대한 세부 정보를 제공합니다.

## AWS 지역

![AWS 지역을 보여 주는 다이어그램](../assets/aws-regions.png){zoomable="yes"}

>[!NOTE]
>
> 중국과 러시아에만 온프레미스.

## Azure 지역

![Azure 지역을 보여 주는 다이어그램](../assets/azure-regions.png){zoomable="yes"}

>[!NOTE]
>
> 중국과 러시아에만 온프레미스. 통합 환경이 필요한 모든 판매자는 미국 지역을 사용해야 합니다.

## Adobe Commerce 설명서

Commerce on cloud infrastructure 안내서에서는 사용자가 Adobe Commerce 애플리케이션에 대한 작업 지식과 이해가 있다고 가정합니다. 아래의 [!DNL Commerce] 개발자 및 사용 안내서를 참조하십시오.

- [Adobe Commerce 개발자 설명서](https://developer.adobe.com/commerce/docs/)&#x200B;(Adobe Developer 사이트) - 고급 기능 개발, 사용자 지정, 통합, 확장 및 사용

- [Adobe Commerce 설명서](https://experienceleague.adobe.com/docs/commerce.html)&#x200B;(Adobe Experience League) - [!DNL Commerce] 프로젝트를 계획, 구현, 운영, 업그레이드 및 유지 관리합니다.

{{$include /help/_includes/templated/whats-new.md}}

<!-- Last updated from includes: 2026-07-13 20:49:04 -->
