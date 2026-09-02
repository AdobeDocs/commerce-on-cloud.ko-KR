---
title: Redis 서비스 설정
description: 클라우드 인프라에서 Adobe Commerce을 위한 백엔드 캐시 솔루션으로서 Redis를 설정하고 최적화하는 방법에 대해 알아봅니다.
feature: Cloud, Cache, Services
exl-id: be6f2462-0878-47e3-b906-ebdd4aa319f2
TQID: https://experienceleague.adobe.com/Q3w1Y1sRuQSwqmbxGfEBavrvHe0ecI9qWJjsfVc2yPU
product_v2:
  - id: eadea719-cf89-469b-a6fd-a236a7138047
feature_v2:
  - id: dac87252-6066-4d6e-a9d2-f6d84c323de7
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2:
  - id: b5ce8718-c3af-4fdb-a1a9-fca32f83a87c
  - id: c1579802-ddd4-4214-8a91-97b2066abe11
source-git-commit: df2792f9d653c4561e4e40cbc71499095f63ff71
workflow-type: tm+mt
source-wordcount: 710
ht-degree: 0%

---

# Redis 서비스 설정

[Redis](https://redis.io)은(는) Adobe Commerce에서 기본적으로 사용하는 `Zend Framework Zend_Cache_Backend_File`을(를) 대체하는 선택적 백엔드 캐시 솔루션입니다.

>[!IMPORTANT]
>
>Redis 캐시는 Adobe Commerce 2.4.9 또는 2.4.5-p16, 2.4.6-p14, 2.4.7-p9 및 2.4.8-p4 이상의 패치 릴리스에서는 지원되지 않습니다. Redis가 지원되지 않는 캐시 구성에 [Valkey](valkey.md)을(를) 사용합니다. 릴리스별로 지원되는 캐시 서비스에 대해서는 [시스템 요구 사항](https://experienceleague.adobe.com/ko/docs/commerce-operations/installation-guide/system-requirements)을 참조하십시오.

{{service-instruction}}

## Redis 사용

Redis를 사용하려면 다음 파일을 업데이트하십시오.

- `.magento/services.yaml`
- `.magento.app.yaml`

### 서비스 구성

`.magento/services.yaml`에서 Redis 서비스 정의를 추가합니다. `<version>`을(를) Adobe Commerce 버전 및 현재 클라우드 템플릿에서 지원하는 Redis 버전으로 바꾸십시오.

```yaml
cache:
  type: redis:<version>
```

예를 들어 Redis 7.2를 지원하는 Commerce 릴리스 및 클라우드 템플릿의 경우:

```yaml
cache:
  type: redis:7.2
```

예제 버전이 일반적이지 않습니다. 실제 기본 및 지원되는 서비스 버전은 Adobe Commerce 버전, 패치 수준 및 현재 클라우드 템플릿에 따라 다릅니다. [시스템 요구 사항](https://experienceleague.adobe.com/ko/docs/commerce-operations/installation-guide/system-requirements)에서 지원되는 조합과 현재 프로젝트 템플릿을 확인하십시오.

### 서비스 관계 구성

`.magento.app.yaml`에서 응용 프로그램과 Redis 서비스 간의 관계를 구성합니다.

```yaml
runtime:
  extensions:
    - redis

relationships:
  redis: "cache:redis"
```

관계 키 `redis`은(는) 응용 프로그램에서 서비스에 액세스하는 데 사용하는 이름입니다. 값 `cache:redis`은(는) `.magento/services.yaml`에 정의된 서비스 ID(`cache`) 및 서비스 유형(`redis`)으로 구성됩니다.

### 변경 사항 커밋 및 배포

구성 변경 사항을 추가, 커밋 및 푸시합니다.

```terminal
git add .magento/services.yaml .magento.app.yaml
git commit -m "Enable Redis service"
git push origin <branch-name>
```

배포가 완료되면 Redis 서비스 관계를 사용할 수 있는지 확인합니다.

{{service-change-tip}}

## 서비스 관계 확인

구성을 배포한 후 응용 프로그램 컨테이너에서 다음 명령을 실행하여 디코딩된 `MAGENTO_CLOUD_RELATIONSHIPS` 개체를 표시합니다.

SSH를 사용하여 원격 클라우드 환경에 연결한 다음 다음을 실행합니다.

```terminal
echo "$MAGENTO_CLOUD_RELATIONSHIPS" | base64 -d | json_pp
```

이 명령은 구성된 모든 서비스 관계를 표시합니다. Redis 연결 세부 정보를 식별하려면 `redis` 관계를 찾으십시오.

다음 약식 예제는 `redis` 관계를 보여 줍니다. 유니버설 스키마가 아닙니다.

```json
{
   "database" : [
      {
         "host" : "database.internal",
         "port" : 3306,
         "path" : "main",
         "scheme" : "mysql"
      }
   ],
   "opensearch" : [
      {
         "host" : "opensearch.internal",
         "port" : 9200,
         "path" : null,
         "scheme" : "http"
      }
   ],
   "redis" : [
      {
         "host" : "redis.internal",
         "port" : 6379,
         "path" : null,
         "scheme" : "redis"
      }
   ]
}
```

출력은 환경 및 서비스 구성에 따라 달라집니다. 이 예제에서 호스트 이름, 포트, IP 주소, 클러스터 이름, 서비스 버전, 사용자 이름 또는 암호를 하드 코딩하지 마십시오. 대상 환경에서 `MAGENTO_CLOUD_RELATIONSHIPS`이(가) 반환한 값을 사용합니다.

`jq`을(를) 사용할 수 있는 경우 다음 명령을 사용하여 Redis 관계만 표시합니다.

```terminal
printf '%s' "$MAGENTO_CLOUD_RELATIONSHIPS" \
  | base64 -d \
  | jq '{redis: .redis}'
```

서비스 관계에 대한 자세한 내용은 [서비스 구성](services-yaml.md)을 참조하세요.

## Redis 구성 사용자 지정

캐시, 세션, L2 및 복제본 연결 권장 사항에 대해서는 _구현 플레이북 모범 사례 안내서_&#x200B;에서 [Valkey 및 Redis 서비스 구성에 대한 모범 사례](https://experienceleague.adobe.com/ko/docs/commerce-operations/implementation-playbook/best-practices/planning/redis-valkey-service-configuration)를 참조하십시오.

## Redis CLI 사용

Redis 관계 이름이 `redis`이라고 가정할 경우 `MAGENTO_CLOUD_RELATIONSHIPS`에서 반환된 호스트 및 포트를 사용하여 Redis에 연결합니다.

Redis가 설치 및 구성된 환경에 연결하고 다음 명령을 실행합니다.

```terminal
redis-cli -h <host> -p <port>
```

**예**

```terminal
redis-cli -h redis.internal -p 6379
```

## 설치된 Redis 버전 가져오기

>[!BEGINTABS]

>[!TAB 통합 환경]

통합 환경에서 `redis` 관계에서 반환된 호스트 및 포트를 사용하여 다음을 실행합니다.

```terminal
redis-cli -h <host> -p <port> info | grep version
```

**응답 예**

```text
redis_version:<installed-version>
gcc_version:<gcc-version>
```

버전 및 빌드 세부 사항은 환경에 따라 다릅니다. 표시된 예제 버전을 필수 또는 범용 서비스 버전으로 취급하지 마십시오.

>[!TAB Pro 스테이징 및 프로덕션]

Pro 스테이징 및 프로덕션 환경에서 다음을 실행합니다.

```terminal
redis-server -v
```

**응답 예**

```text
Redis server v=<installed-version> ...
```

버전 및 빌드 세부 사항은 환경에 따라 다릅니다. 표시된 예제 버전을 필수 또는 범용 서비스 버전으로 취급하지 마십시오.

>[!ENDTABS]

## Redis 문제 해결

Redis 문제 해결에 대한 도움말은 다음 Adobe Commerce 지원 문서를 참조하십시오.

- [Adobe Commerce에 대한 관리 경고: Redis 메모리 경고 경고](https://experienceleague.adobe.com/ko/docs/commerce-operations/tools/managed-alerts-for-adobe-commerce/managed-alerts-on-magento-commerce-redis-memory-warning-alert)
- [Adobe Commerce에 대한 관리 경고: Redis 메모리 위험 경고](https://experienceleague.adobe.com/ko/docs/commerce-operations/tools/managed-alerts-for-adobe-commerce/managed-alerts-on-magento-commerce-redis-memory-critical-alert)

### Valkey가 구성한 캐시에 대한 캐시 정리 오류 참조 Redis

`cache` 서비스가 Valkey로 구성된 경우에도 배포 전 캐시 정리 실패가 오류 코드 `[107]`(`clean-redis-cache`)과(와) `Connection to Redis` 메시지를 표시할 수 있습니다. `ece-tools`은(는) `cache` 관계를 지원하는 서비스에 관계없이 캐시 정리 단계에 이 레거시 Redis 기반 오류 코드와 메시지를 사용하므로 Redis가 설치되었음을 나타내는 단어가 아닙니다.

기본 오류가 관계 호스트의 `Name or service not known`과 같은 DNS 오류인 경우 서비스 관계를 사용하기 전에 배포 단계가 실행되었거나 `.magento.app.yaml`의 관계 이름이 `.magento/services.yaml`의 서비스 ID와 일치하지 않습니다. [서비스 관계 확인](#verify-the-service-relationship)을 참조하세요.
