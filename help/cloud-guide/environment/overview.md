---
title: 구성 파일 개요
description: 맞춤화된 Adobe Commerce 스토어의 배포 및 관리를 지원하도록 클라우드 인프라 환경을 구성하는 방법에 대해 알아봅니다.
feature: Cloud, Configuration, Services, Iaas, Paas
exl-id: 305380b0-1920-4037-a1db-80e72c6af333
TQID: 'https://experienceleague.adobe.com/mFjzrTN6R7LC3e9ADnzzulcWAwun4k-g3aCjc9Bo3gQ'
product_v2:
  - id: eadea719-cf89-469b-a6fd-a236a7138047
    internal-label: Commerce
feature_v2:
  - id: dac87252-6066-4d6e-a9d2-f6d84c323de7
    internal-label: Configuration
  - id: 5a951749-fac9-5bc7-9a98-ebe4ff066437
    internal-label: Cloud
  - id: da76473c-f99b-5ad0-9b14-896aed473f8a
    internal-label: Services
  - id: 00451af3-7b97-5414-9992-3a6c269e413f
    internal-label: Paas
  - id: e8818fe6-9c8b-4bc0-9ef8-377a10b7bc75
    internal-label: Architecture
subfeature_v2:
  - id: df5e974b-6742-4873-a687-a6bedaafdaa2
    internal-label: IaaS
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
    internal-label: Admin
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
    internal-label: Developer
source-git-commit: e6e0bd8e116b2f0b93557b6aeb2aac7cbb8e1d8a
workflow-type: tm+mt
source-wordcount: '286'
ht-degree: 0%
---
# 구성 파일 개요

Adobe Commerce on cloud infrastructure의 환경에는 Adobe Commerce 애플리케이션 코드베이스 및 파일을 위한 완전한 시스템을 제공하기 위한 애플리케이션, 서비스 및 데이터베이스가 포함된 컨테이너가 포함됩니다.

다음 구성 파일을 사용하여 프로젝트 환경을 지원하도록 애플리케이션 설정, 경로, 빌드 및 배포 작업, 알림을 구성할 수 있습니다.

| 구성 | 파일 이름 | 설명 |
| ------------- | -------- | ----------- |
| [응용 프로그램](../application/configure-app-yaml.md) | `.magento.app.yaml` | 서비스, 후크 및 cron 작업을 포함하여 Adobe Commerce을 빌드하고 배포하는 방법을 정의합니다. |
| [환경](configure-env-yaml.md) | `.magento.env.yaml` | 환경 변수를 사용하여 Pro Staging 및 프로덕션을 포함하여 모든 환경에서 빌드 및 배포 작업을 중앙 집중화합니다. |
| [경로](../routes/routes-yaml.md) | `.magento/routes.yaml` | 캐싱, 리디렉션 및 서버측 포함을 구성합니다. |
| [서비스](../services/services-yaml.md) | `.magento/services.yaml` | Adobe Commerce이 사용하는 서비스를 이름 및 버전별로 정의합니다. 예를 들어, 이 파일에는 MariaDB, PHP 확장, Redis 또는 Valkey, RabbitMQ 및 Elasticsearch 또는 OpenSearch 버전이 포함될 수 있습니다. 이러한 변경 사항을 Pro 계획 스테이징 및 프로덕션 환경에 푸시하려면 지원 티켓을 열어야 합니다. |
| [PHP 설정](../application/php-settings.md#configure-php) | `php.ini` | 프로젝트에 추가할 수 있는 선택적 파일입니다. 이 파일에 포함된 설정은 클라우드 인프라에서 유지 관리하는 설정에 추가됩니다. |

{style="table-layout:auto"}

## Pro 환경에 대한 구성 업데이트

클라우드 인프라 Pro 스테이징 및 프로덕션 환경의 Adobe Commerce의 경우 로컬 개발 환경에서 많은 구성 옵션을 업데이트하고 변경 사항을 커밋하여 이러한 환경에 적용할 수 있습니다. 그러나 다음 구성 옵션을 업데이트하려면 [Adobe Commerce 지원 티켓을 제출](https://experienceleague.adobe.com/en/docs/support-resources/adobe-support-tools-guide/adobe-commerce-support/adobe-commerce-help-center-user-guide#submit-ticket)해야 합니다.

- `.magento/services.yaml` 파일에서 서비스를 설치하거나 업데이트합니다.
- `.magento.app.yaml` 파일에서 `mounts` 및 `disk` 속성에 대한 구성을 변경합니다.

{{pro-self-service-warning}}
