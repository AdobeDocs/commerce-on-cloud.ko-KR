---
title: 서비스 구성
description: MySQL, Redis 및 Elasticsearch과 같은 클라우드 인프라에서 Adobe Commerce에서 사용하는 서비스를 구성하는 방법에 대해 알아봅니다.
feature: Cloud, Configuration, Services
exl-id: ddf44b7c-e4ae-48f0-97a9-a219e6012492
TQID: https://experienceleague.adobe.com/qvCjqNc8E9QGme-zM42vMg-kb1WjwTlWUqjbm-NI2bg
product_v2:
  - id: eadea719-cf89-469b-a6fd-a236a7138047
feature_v2:
  - id: ba9e5be9-7de1-4f71-a5d2-baead0e425ee
  - id: dac87252-6066-4d6e-a9d2-f6d84c323de7
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2:
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: 660f62d8bb809675ec872da2e87301308d333ba8
workflow-type: tm+mt
source-wordcount: 1176
ht-degree: 0%

---

# 서비스 구성

`services.yaml` 파일은 MySQL, Redis 또는 Valkey, Elasticsearch 또는 OpenSearch와 같은 클라우드 인프라에서 Adobe Commerce이 지원하고 사용하는 서비스를 정의합니다. 외부 서비스 공급자에 가입할 필요가 없습니다.

>[!NOTE]
>
>`.magento/services.yaml` 파일은 프로젝트의 `.magento` 디렉터리에서 로컬로 관리됩니다. 배포 중에 클라우드 인프라의 Adobe Commerce은 이 구성을 사용하여 대상 환경에 지원되는 서비스를 프로비저닝합니다. 배포 후 `.magento` 디렉터리가 원격 서버에서 제거되었으므로 배포된 환경에 `services.yaml`이(가) 없습니다.

배포 스크립트는 `.magento` 디렉터리의 구성 파일을 사용하여 구성된 서비스로 환경을 프로비전합니다. 응용 프로그램이 `.magento.app.yaml` 파일의 [`relationships`](../application/properties.md#relationships) 속성에 포함된 경우 해당 응용 프로그램에서 서비스를 사용할 수 있게 됩니다. `services.yaml` 파일에 _type_ 및 _disk_ 값이 있습니다. 서비스 유형은 서비스 _name_ 및 _version_&#x200B;을(를) 정의합니다.

`.magento/services.yaml`의 서비스 구성이 `composer.json`에 정의된 PHP 및 Composer 패키지 종속성과 분리되어 `composer.lock`에 잠겨 있습니다.

## 서비스 변경 사항이 적용되는 위치

서비스 구성을 변경하면 배포에서 업데이트된 서비스로 환경을 프로비저닝하게 되며, 이는 다음 환경에 영향을 줍니다.

- 프로덕션 `master`을(를) 포함한 모든 시작 환경
- Pro 통합 환경

{{$include /help/_includes/pro-services-support.md}}

## 기본 및 지원 서비스

Adobe Commerce on cloud infrastructure는 프로젝트에 대해 구성할 수 있는 다음 서비스를 지원합니다.

- [액티브MQ](activemq.md)
- [MySQL](mysql.md)
- [Redis](redis.md) 또는 [Valkey](valkey.md)
- [래빗MQ](rabbitmq.md)
- [Elasticsearch](elasticsearch.md)
- [OpenSearch](opensearch.md)

>[!NOTE]
>[사용 가능한 버전 간에 순차적으로 RabbitMQ를 업그레이드합니다](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/configure/service/rabbitmq#upgrading-the-rabbitmq-service). 예를 들어 3.9에서 4.1로 바로 업그레이드하지 마십시오.
>
>새 버전으로 업그레이드한 후 RabbitMQ에서 사용자 지정 메시지 대기열을 다시 만들려면 전체 배포를 트리거합니다.

## 구성된 서비스 및 버전 보기

현재 템플릿 [`services.yaml` 파일](https://github.com/magento/magento-cloud/blob/master/.magento/services.yaml)에서 예제 서비스 정의 및 디스크 값을 볼 수 있습니다. 실제 기본 및 지원되는 서비스 버전은 Adobe Commerce 버전과 현재 클라우드 템플릿에 따라 다릅니다.

다음 예제에서는 `services.yaml` 구성 파일에 서비스 정의를 표시합니다.

```yaml
mysql:
    type: mysql:11.8
    disk: 5120

cache:
    type: valkey:9.0

opensearch:
    type: opensearch:3  # minor version not required; uses latest
    disk: 1024

rabbitmq:
    type: rabbitmq:4.3
    disk: 1024

activemq-artemis:
    type: activemq-artemis:2.42
    disk: 1024
```

## 서비스 값

서비스 ID 및 서비스 유형 구성 `type: <name>:<version>`을(를) 제공합니다. 서비스가 영구 스토리지를 사용하는 경우 디스크 값을 제공해야 합니다.

다음 형식을 사용하십시오.

```yaml
<service-id>:
    type: <name>:<version>
    disk: <value-MB>
```

### `service-id`

`service-id` 값은 프로젝트에서 서비스를 식별합니다. `a`에서 `z`까지, `0`에서 `9`까지 영숫자만 사용할 수 있습니다(예: `valkey`).

이 _service-id_ 값은 `.magento.app.yaml` 구성 파일의 [`relationships`](../application/properties.md#relationships) 속성에 사용됩니다.

```yaml
relationships:
    valkey: "valkey:valkey"
```

각 서비스 유형의 여러 인스턴스에 이름을 지정할 수 있습니다. 예를 들어 세션용 인스턴스 및 캐시용 인스턴스 등 여러 개의 Valkey 인스턴스를 사용할 수 있습니다.

```yaml
valkey:
    type: valkey:<version>

valkey2:
    type: valkey:<version>
```

`services.yaml` 파일의 서비스 이름 바꾸기:

- 지정한 새 이름으로 서비스를 만들기 전의 기존 서비스입니다.
- 서비스에 대한 기존 데이터가 모두 제거됩니다. Adobe에서는 기존 서비스의 이름을 변경하기 전에 [스타터 환경을 백업](../storage/snapshots.md)하는 것이 좋습니다.

### `type`

`type` 값은 서비스 이름과 버전을 지정합니다. For example:

```yaml
mysql:
    type: mysql:10.4
```

### `disk`

`disk` 값은 서비스에 할당할 영구 디스크 저장소 크기(MB)를 지정합니다. MySQL과 같은 영구 저장소를 사용하는 서비스는 디스크 값을 제공해야 합니다. Valkey와 같이 영구 저장소 대신 메모리를 사용하는 서비스에는 디스크 값이 필요하지 않습니다.

```yaml
mysql:
    type: mysql:10.4
    disk: 5120
```

프로젝트당 현재 기본 스토리지 용량은 5GB 또는 5,120MB입니다. 이 금액을 애플리케이션과 각 서비스 간에 분배할 수 있습니다.

## 서비스 관계

클라우드 인프라 프로젝트의 Adobe Commerce에서 `.magento.app.yaml` 파일에 구성된 서비스 [관계](../application/properties.md#relationships)은(는) 애플리케이션에서 사용할 수 있는 서비스를 결정합니다.

[`$MAGENTO_CLOUD_RELATIONSHIPS`](../environment/variables-cloud.md) 환경 변수에서 모든 서비스 관계에 대한 구성 데이터를 검색할 수 있습니다. 구성 데이터에는 포트 번호 및 로그인 자격 증명과 같은 필수 연결 세부 정보와 함께 서비스 이름, 유형 및 버전이 포함됩니다.

### 로컬 개발 환경에서 관계 확인

1. 로컬 개발 환경에서 활성 환경의 관계를 표시합니다.

   ```bash
   magento-cloud relationships
   ```

1. 응답에서 `service` 및 `type`을(를) 확인합니다. 이 응답은 IP 주소 및 포트 번호와 같은 연결 정보를 제공합니다.

   >축약된 샘플 응답

   ```yaml
   valkey:
       -
   ...
           type: 'valkey:8.0'
           port: 6379
   opensearch:
       -
   ...
           type: 'opensearch:3'
           port: 9200
   database:
       -
   ...
           type: 'mysql:11.8'
           port: 3306
   ```

### 원격 환경에서의 관계 확인

1. SSH를 사용하여 원격 환경에 로그인합니다.

1. 환경에 구성된 모든 서비스의 관계 구성 데이터를 나열합니다.

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   또는 다음 `ece-tools` 명령을 사용하여 관계를 봅니다.

   ```bash
   php ./vendor/bin/ece-tools env:config:show services
   ```

1. 응답에서 `service` 및 `type`을(를) 확인합니다. 응답에서는 IP 주소, 포트 번호, 필요한 사용자 이름 및 암호 자격 증명과 같은 연결 정보를 제공합니다.

## 서비스 버전

클라우드 인프라에서 배포되고 테스트된 버전은 클라우드 인프라에서 Adobe Commerce에 대한 서비스 버전 및 호환성 지원을 결정합니다. 이는 Adobe Commerce 온프레미스 배포에서 지원하는 버전과 다른 경우가 있습니다. Adobe이 특정 Adobe Commerce 및 Magento Open Source 릴리스에서 테스트한 타사 소프트웨어 종속성 목록은 _설치_ 안내서의 [시스템 요구 사항](https://experienceleague.adobe.com/en/docs/commerce-operations/installation-guide/system-requirements)을 참조하십시오.

### 소프트웨어 EOL 확인

배포 프로세스 중에 `ece-tools` 패키지는 각 서비스의 EOL(서비스 종료) 날짜에 대해 설치된 서비스 버전을 확인합니다.

- 서비스 버전이 EOL 날짜로부터 3개월 이내인 경우 배포 로그에 알림이 표시됩니다.
- EOL 날짜가 과거인 경우 경고 알림이 표시됩니다.

저장소 보안을 유지하려면 설치된 소프트웨어 버전이 EOL에 도달하기 전에 업데이트하십시오. [ece-tools&#39; `eol.yaml` 파일](https://github.com/magento/ece-tools/blob/develop/config/eol.yaml)에서 EOL 날짜를 검토할 수 있습니다.

### OpenSearch로 마이그레이션

{{elasticsearch-support}}

Adobe Commerce 버전 2.4.4 이상은 [OpenSearch 서비스 설정](opensearch.md)을 참조하십시오.

## 서비스 버전 변경

설치된 서비스 버전을 업그레이드하여 클라우드 환경에 배포된 Adobe Commerce 버전과의 호환성을 유지할 수 있습니다.

설치된 서비스의 서비스 버전을 직접 다운그레이드할 수 없습니다. 그러나 필요한 버전으로 서비스를 만들 수는 있습니다. [다운그레이드 서비스 버전](#downgrade-version)을 참조하세요.

### 설치된 서비스 버전 업그레이드

`services.yaml` 파일에서 서비스 구성을 업데이트하여 설치된 서비스 버전을 업그레이드할 수 있습니다.

1. `.magento/services.yaml` 파일에서 서비스에 대한 [`type`](#type) 값을 변경합니다.

   > 원래 서비스 정의

   ```yaml
   mysql:
       type: mysql:11.8
       disk: 2048
   ```

   > 업데이트된 서비스 정의

   ```yaml
   mysql:
       type: mysql:12.3
       disk: 5120
   ```

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add .magento/services.yaml
   ```

   ```bash
   git commit -m "Upgrade MySQL from MariaDB 11.8 to 12.3."
   ```

   ```bash
   git push origin <branch-name>
   ```

### 다운그레이드 버전

설치된 서비스를 직접 다운그레이드할 수는 없습니다. 다음 두 가지 옵션이 있습니다.

1. 기존 서비스의 이름을 새 버전으로 바꾸십시오. 새 버전으로 바꾸면 기존 서비스와 데이터가 제거되고 새 서비스가 추가됩니다.

1. 서비스를 만들고 기존 서비스의 데이터를 저장합니다.

서비스 버전을 변경할 때는 `services.yaml` 파일에서 서비스 구성을 업데이트하고 `.magento.app.yaml` 파일에서 관계를 업데이트해야 합니다.

#### 기존 서비스의 이름을 변경하여 서비스 버전 다운그레이드

1. `.magento/services.yaml` 파일에서 기존 서비스의 이름을 바꾸고 버전을 변경합니다.

   >[!WARNING]
   >
   >기존 서비스의 이름을 바꾸면 기존 서비스가 바뀌고 모든 데이터가 삭제됩니다. 데이터를 유지해야 하는 경우 기존 서비스의 이름을 바꾸는 대신 서비스를 만듭니다.

   예를 들어 _mysql_ 서비스에 대한 MariaDB 버전을 버전 10.4에서 10.3으로 다운그레이드하려면 기존 _service-id_ 및 _type_ 구성을 변경하십시오.

   > 원래 `services.yaml` 정의

   ```yaml
   mysql:
       type: mysql:10.4
       disk: 5120
   ```

   > 새 `services.yaml` 정의

   ```yaml
   mysql2:
        type: mysql:10.3
        disk: 5120
   ```

1. `.magento.app.yaml` 파일의 관계를 업데이트합니다.

   > 원래 `.magento.app.yaml` 구성

   ```yaml
   relationships:
       database: "mysql:mysql"
   ```

   > `.magento.app.yaml` 구성을 업데이트했습니다.

   ```yaml
   relationships:
       database: "mysql2:mysql"
   ```

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

#### 서비스를 생성하여 서비스 다운그레이드

1. 다운그레이드된 버전 사양으로 프로젝트의 `services.yaml` 파일에 서비스 정의를 추가합니다. 다음 예제에서는 _mysql2_&#x200B;을(를) 참조하십시오.

   > services.yaml

   ```yaml
   mysql:
       type: mysql:10.4
       disk: 5120
   mysql2:
       type: mysql:10.3
       disk: 5120
   ```

1. 새 서비스를 사용하려면 `.magento.app.yaml` 파일에서 관계 구성을 변경하십시오.

   > 원래 `.magento.app.yaml` 구성

   ```yaml
   relationships:
       database: "mysql:mysql"
   ```

   > 새 `.magento.app.yaml` 구성

   ```yaml
   relationships:
       database: "mysql2:mysql"
   ```

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.
