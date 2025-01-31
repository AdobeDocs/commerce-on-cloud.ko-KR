---
title: Pro 아키텍처
description: Pro 아키텍처에서 지원하는 환경에 대해 알아봅니다.
feature: Cloud, Auto Scaling, Iaas, Paas, Storage
topic: Architecture
source-git-commit: 0d9d3d64cd0ad4792824992af354653f61e4388d
workflow-type: tm+mt
source-wordcount: '1554'
ht-degree: 0%

---

# Pro 아키텍처

Adobe Commerce on cloud infrastructure Pro 아키텍처는 스토어를 개발, 테스트 및 시작하는 데 사용할 수 있는 여러 환경을 지원합니다.

- **기본**—PaaS(서비스) 컨테이너로 배포된 `master` 분기를 제공합니다.
- **통합** - 개발을 위한 단일 `integration` 분기를 제공하지만, 분기 하나를 추가로 만들 수도 있습니다. 이렇게 하면 PaaS(Platform as a Service) 컨테이너에 배포된 최대 2개의 _active_ 분기를 사용할 수 있습니다.
- **스테이징**—전용 IaaS(Infrastructure as a Service) 컨테이너에 배포된 단일 `staging` 분기를 제공합니다.
- **프로덕션** - 전용 IaaS(Infrastructure as a Service) 컨테이너에 배포된 단일 `production` 분기를 제공합니다.

다음 표에는 환경 간의 차이점이 요약되어 있습니다.

|                                        | 통합 | 스테이징 | 프로덕션 |
| -------------------------------------- | ----------- | ----------------- | -------------------- |
| [!DNL Cloud Console]에서 설정 관리를 지원합니다. | 예 | 제한적 | 제한적 |
| 여러 분기 지원 | 예 | 아니요(스테이징만) | 아니요(프로덕션 전용) |
| 구성에 YAML 파일 사용 | 예 | 아니요 | 아니요 |
| 전용 IaaS 하드웨어에서 실행 | 아니요 | 예 | 예 |
| Fastly CDN 포함 | 아니요 | 예 | 예 |
| New Relic 서비스 포함 | 아니요 | APM | APM + NRI |
| 자동 백업 | 아니요 | 예 | 예 |

>[!NOTE]
>
>Adobe은 Adobe Commerce 프로젝트를 개발 및 테스트할 수 있도록 로컬 Cloud Docker 환경에 배포할 Commerce용 Cloud Docker 도구를 제공합니다. [Docker 개발](../dev-tools/cloud-docker.md)을 참조하세요.

## 환경 아키텍처

프로젝트는 기본 환경 분기 `integration`, `staging` 및 `production`이(가) 세 개 있는 단일 Git 저장소입니다. 다음 다이어그램은 Pro 환경의 계층적 관계를 보여 줍니다.

![Pro 환경 아키텍처에 대한 높은 수준의 보기](../../assets/pro-branch-architecture.png)

### 기본 환경

Pro 프로젝트에서 `master` 분기는 프로덕션 환경과 함께 활성 PaaS 환경을 제공합니다. 서비스를 중단하지 않고 프로덕션 환경을 디버깅할 수 있도록 항상 프로덕션 코드의 복사본을 `master` 환경에 푸시합니다.

**주의 사항:**

- `master` 분기를 기반으로 분기를 **만들지**&#x200B;마십시오. 통합 환경을 사용하여 개발을 위한 활성 분기를 만듭니다.

- 개발, UAT 또는 성능 테스트에 `master` 환경을 사용하지 마십시오.

### 통합 환경

통합 환경은 PaaS로 알려진 서버 그리드의 Linux 컨테이너(LXC)에서 실행됩니다. 각 환경에는 사이트를 테스트할 웹 서버와 데이터베이스가 포함되어 있습니다. AWS 및 Azure IP 주소 목록은 [지역 IP 주소](../project/regional-ip-addresses.md)를 참조하세요.

**권장 사용 사례:**

통합 환경은 스테이징 및 프로덕션 환경으로 변경 사항을 이동하기 전에 제한된 테스트 및 개발을 위해 설계되었습니다. 예를 들어 통합 환경을 사용하여 다음 작업을 완료할 수 있습니다.

- CI(지속적 통합) 프로세스에 대한 변경 사항이 클라우드와 호환되는지 확인

- 홈, 카테고리, PDP(제품 세부 사항 페이지), 체크아웃 및 관리자와 같은 주요 페이지에서 중요한 워크플로우를 테스트합니다

통합 환경에서 최상의 성능을 얻으려면 다음 모범 사례를 따르십시오.

- 카탈로그 크기 제한 - 참고로 샘플 데이터에는 약 2,048개의 제품이 포함되어 있습니다. 카탈로그 크기를 약 4,000~5,000개의 제품으로 줄여 보십시오.
카탈로그의 제품 수를 확인하려면 다음 MySQL 쿼리를 실행합니다.

  ```sql
  select distinct count(entity_id) from catalog_product_entity;
  ```

- 고객 그룹 수 감소 - 고객 그룹이 너무 많으면 색인화 성능 및 전체 성능에 영향을 줄 수 있습니다.

- 1명 또는 2명의 동시 사용자로 사용 제한

- cron 작업을 비활성화하고 필요에 따라 수동으로 실행

**주의 사항:**

- 통합 환경에서는 Fastly CDN 및 New Relic 서비스에 액세스할 수 없습니다

- 통합 환경 아키텍처가 스테이징 및 프로덕션 아키텍처와 일치하지 않습니다.

- 개발 테스트, 성능 테스트 또는 UAT(사용자 승인 테스트)에 `integration` 환경을 사용하지 마십시오.

- `integration` 환경을 사용하여 Adobe Commerce 기능에 대한 B2B를 테스트하지 마십시오.

- 통합 환경의 데이터베이스를 데이터베이스 프로덕션 또는 스테이징에서 복원할 수 없습니다

{{enhanced-integration-envs}}

### 스테이징 환경

스테이징 환경은 사이트를 테스트하기 위한 프로덕션 환경에 가깝습니다. 전용 IaaS 하드웨어에서 호스팅되는 이 환경에는 Fastly CDN, New Relic APM 및 검색과 같은 모든 서비스가 포함됩니다.

**권장 사용 사례:**

환경은 프로덕션 아키텍처와 일치하며 `production` 환경에 기능을 푸시하기 전에 UAT, 콘텐츠 스테이징 및 최종 검토를 위해 설계되었습니다. 예를 들어 `staging` 환경을 사용하여 다음 작업을 완료할 수 있습니다.

- 프로덕션 데이터에 대한 회귀 테스트

- Fastly 캐싱을 사용한 성능 테스트

- 프로덕션에서 패치 대신 새 빌드 테스트

- 새 빌드에 대한 UAT 테스트

- Adobe Commerce용 B2B 테스트

- cron 구성 사용자 지정 및 cron 작업 테스트

[배포 워크플로](pro-develop-deploy-workflow.md#deployment-workflow) 및 [배포 테스트](../test/staging-and-production.md)를 참조하세요.

**주의 사항:**

- 프로덕션 사이트를 시작한 후 주로 스테이징 환경을 사용하여 프로덕션에 중요한 버그 수정을 위한 패치를 테스트합니다.

- `staging` 분기에서 분기를 만들 수 없습니다. 대신 `integration` 분기에서 `staging` 분기로 코드 변경 내용을 푸시합니다.

{{second-staging}}

### 프로덕션 환경

프로덕션 환경에서는 공개 수준의 단일 및 다중 사이트 스토어를 실행합니다. 이 환경은 이중화된 고가용성 노드를 갖춘 전용 IaaS 하드웨어에서 실행되어 고객을 위한 지속적인 액세스 및 장애 복구 보호 기능을 제공합니다. 프로덕션 환경에는 스테이징 환경의 모든 서비스와 응용 프로그램 데이터 및 성능 분석에 자동으로 연결하여 동적 서버 모니터링을 제공하는 [New Relic 인프라(NRI)](../monitor/new-relic-service.md#new-relic-infrastructure) 서비스가 포함됩니다.

**주의:**

`production` 분기에서 분기를 만들 수 없습니다. 대신 `staging` 분기에서 `production` 분기로 코드 변경 내용을 푸시합니다.

### 프로덕션 기술 스택

프로덕션 환경에는 HAProxy에서 VM당 관리하는 Elastic Load Balancer 뒤에 3개의 VM(가상 컴퓨터)이 있습니다. 각 VM에는 다음 기술이 포함되어 있습니다.

- **Fastly CDN**—HTTP 캐싱 및 CDN

- **NGINX**—PHP-FPM을 사용하는 웹 서버, 여러 작업자가 있는 인스턴스 하나

- **GlusterFS**—모든 정적 파일 배포 및 4개의 디렉터리 마운트를 사용한 동기화를 관리하기 위한 파일 서버:

   - `var`
   - `pub/media`
   - `pub/static`
   - `app/etc`

- **Redis**—VM당 하나의 서버만 활성 상태이고 나머지 두 서버는 복제본입니다.

- **Elasticsearch**—cloud infrastructure 2.2 - 2.4.3-p2에서 Adobe Commerce 검색

- **OpenSearch**—클라우드 인프라 2.3.7-p3, 2.4.3-p2, 2.4.4 이상에서 Adobe Commerce 검색

- **Galera**—노드당 하나의 MariaDB MySQL 데이터베이스가 있는 데이터베이스 클러스터로, 모든 데이터베이스의 고유 ID에 대해 3의 자동 증분 설정을 사용합니다.

다음 그림은 프로덕션 환경에서 사용되는 기술을 보여 줍니다.

![프로덕션 기술 스택](../../assets/az-stack-diagram.png)

## 이중 하드웨어

클라우드 인프라의 Adobe Commerce은 기존의 능동-수동 `master` 또는 기본-보조 설정을 실행하는 대신 세 인스턴스가 모두 읽기 및 쓰기를 허용하는 _이중 아키텍처_&#x200B;를 실행합니다. 이 아키텍처는 확장 시 다운타임 없이 작동하며 트랜잭션 무결성을 보장합니다.

Adobe은 고유한 이중화 하드웨어로 인해 게이트웨이 서버를 3대 제공할 수 있습니다. 허용 목록에 추가하다 대부분의 외부 서비스를 사용하면 하나의 고정 IP에 여러 개의 IP 주소를 추가할 수 있으므로 둘 이상의 IP 주소를 갖는 것은 문제가 되지 않습니다. 세 개의 게이트웨이는 프로덕션 환경 클러스터의 세 서버에 매핑되며 고정 IP 주소를 유지합니다. 완전히 이중화되어 있으며 모든 수준에서 고가용성이 보장됩니다.

- DNS
- CDN(Content Delivery Network)
- ELB(Elastic Load Balancer)
- 데이터베이스 및 웹 서버를 포함한 모든 Adobe Commerce 서비스로 구성된 3서버 클러스터

## 백업 및 재해 복구

Adobe Commerce on cloud infrastructure는 각 영역에 별도의 데이터 센터가 있는 세 개의 개별 AWS 또는 Azure Availability Zone에서 각 Pro 프로젝트를 복제하는 고가용성 아키텍처를 사용합니다. 이러한 중복성 외에도 Pro 스테이징 및 프로덕션 환경에는 _치명적인 오류_&#x200B;의 경우에 사용하도록 설계된 정기적인 라이브 백업이 제공됩니다.

**자동 백업**&#x200B;에는 MySQL 데이터베이스 및 탑재된 볼륨에 저장된 파일과 같이 실행 중인 모든 서비스의 영구 데이터가 포함됩니다. 백업은 프로덕션 환경과 동일한 영역에 있는 암호화된 EBS(Elastic Block Storage)에 저장됩니다. 자동 백업은 별도의 시스템에 저장되므로 공개적으로 액세스할 수 없습니다.

>[!NOTE]
>
>마운트된 볼륨에는 [쓰기 가능한 마운트](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/configure/app/properties/properties#mounts)만 포함/참조되며 `app/` 디렉터리의 일부만 포함됩니다. 다른 파일의 경우 [빌드 및 배포 프로세스](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/architecture/pro-develop-deploy-workflow#deployment-workflow)에서 생성/생성되며 나머지 파일에 대해서도 Git 저장소를 확인해야 합니다.

{{pro-backups}}

CLI 명령을 사용하여 스테이징 및 프로덕션 환경에 대한 데이터베이스의 **수동 백업**&#x200B;을 만들 수 있습니다. [데이터베이스 백업](../storage/database-dump.md)을 참조하십시오. `integration` 환경의 경우 Adobe은 클라우드 인프라 프로젝트에서 Adobe Commerce에 액세스한 후 주요 변경 사항을 적용하기 전에 첫 번째 단계로 백업을 만들 것을 권장합니다. [백업 관리](../storage/snapshots.md)를 참조하십시오.

### 복구 지점 목표

마지막 백업까지의 RPO(복구 시점 목표) 시간에 대한 자세한 내용은 Adobe 고객 성공 관리자에게 문의하십시오. 백업 빈도는 계획의 백업 일정과 스토리지 서비스에 쓸 변경 사항의 볼륨에 따라 다릅니다.

### 보존 정책

Adobe은 다음 데이터 보존 정책에 따라 자동 백업을 유지합니다.

| 기간 | 백업 보존 정책 |
| ------------------ | ----------------------- |
| 1일~3일 | 시간당 백업 1회 |
| 4일~7일 | 일일 백업 1회 |
| 2~6주 | 매주 1회 백업 |
| 8~12주 | 격주 백업 1회 |
| 3개월~5개월 | 매월 1회 백업 |

이 정책은 클라우드 인프라 계획에 따라 달라질 수 있습니다.

### 복구 시간 목표

RTO는 스토리지 크기에 따라 다릅니다. 대용량 EBS 볼륨은 리스토어에 더 많은 시간이 소요됩니다. 복원 시간은 데이터베이스 크기에 따라 달라질 수 있습니다. 자세한 내용은 Adobe 고객 성공 관리자에게 문의하십시오.

## Pro 클러스터 확장

Pro 클러스터 크기 조정 및 _compute_ 구성은 선택한 클라우드 공급자(AWS, Azure), 지역 및 서비스 종속성에 따라 다릅니다. Adobe 클라우드 인프라는 수요 변화에 따라 트래픽 기대치 및 서비스 요구 사항을 수용할 수 있도록 Pro 클러스터를 확장할 수 있습니다.

중복 아키텍처는 Adobe 클라우드 인프라를 가동 중지 시간 없이 확장할 수 있도록 지원합니다. 세 가지 인스턴스는 사이트 운영에 영향을 주지 않고 용량을 업그레이드하기 위해 각각 회전합니다. 예를 들어, 제한이 데이터베이스 수준이 아닌 PHP 수준에 있는 경우 기존 클러스터에 웹 서버를 추가할 수 있습니다. 데이터베이스 수준에서 추가 CPU가 제공하는 세로 크기 조절을 보완하는 _가로 크기 조절_&#x200B;을 제공합니다. [조정된 아키텍처](scaled-architecture.md)를 참조하십시오.

이벤트나 기타 이유로 트래픽이 크게 증가할 것으로 예상되면 일시적으로 용량을 증가하도록 요청할 수 있습니다. _Commerce 도움말 센터_&#x200B;에서 [임시 업사이징을 요청하는 방법](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/how-to-request-temporary-magento-upsize.html)을 참조하세요.
