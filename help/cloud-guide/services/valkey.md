---
title: Valkey 서비스 설정
description: Redis 대체 및 캐시 백엔드 설정 맞춤화를 포함하여 클라우드 인프라에서 Adobe Commerce을 위한 백엔드 캐시 솔루션으로서의 Valkey를 설정하고 최적화하는 방법에 대해 알아봅니다.
feature: Cloud, Cache, Services
exl-id: f8933e0d-a308-4c75-8547-cb26ab6df947
TQID: https://experienceleague.adobe.com/-aBnwClJGQlRkEfugtChxbjLObLzTu0xl1IvkYUVRsk
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
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
source-git-commit: d5d947f9858ab15e2e5daed7848163846580f883
workflow-type: tm+mt
source-wordcount: 701
ht-degree: 0%

---

# Valkey 서비스 설정

[Valkey](https://valkey.io)은(는) 클라우드 인프라의 Adobe Commerce에 대한 선택적 백엔드 캐시 솔루션입니다. Adobe Commerce 2.4.9 이상 또는 2.4.5-p16, 2.4.6-p14, 2.4.7-p9 및 2.4.8-p4 이상의 패치 릴리스에서 기본 캐시 구성을 재정의하는 경우 Valkey가 필요합니다.

{{service-instruction}}

## Valkey 구성

Redis를 Valkey로 바꾸려면 다음 파일을 업데이트하십시오.

- `.magento/services.yaml`
- `.magento.app.yaml`

### 서비스 구성

`.magento/services.yaml`에서 Redis 서비스 정의를 Valkey 서비스 정의로 바꾸십시오. `<version>`을(를) Adobe Commerce 버전 및 현재 클라우드 템플릿에서 지원하는 올바른 버전으로 바꾸십시오.

```yaml
cache:
  type: valkey:<version>
```

**예**

```yaml
cache:
  type: valkey:8.0
```

예제 버전이 일반적이지 않습니다. 실제 기본 및 지원되는 서비스 버전은 Adobe Commerce 버전과 현재 클라우드 템플릿에 따라 다릅니다. 현재 프로젝트 템플릿에서 지정한 버전을 사용합니다. 자세한 내용은 [서비스 구성](services-yaml.md#service-versions)을 참조하십시오.

>[!WARNING]
>
>서비스 ID를 변경하면 기존 서비스가 제거되고 새 서비스가 만들어집니다. 제거된 서비스의 기존 데이터는 영구적으로 삭제됩니다. 서비스 이름을 바꾸기 전에 환경을 백업합니다.

`type` 값을 `redis:<version>`에서 `valkey:<version>`(으)로 변경할 때 동일한 서비스 ID를 유지하는 경우에도 캐시 및 세션 데이터가 지속된다고 가정하지 마십시오. 기존 캐시 및 세션 데이터는 보존되지 않으며 마이그레이션이 완료된 후 사용자가 로그아웃되므로 마이그레이션을 새 캐시를 만드는 것으로 취급합니다.

### 서비스 관계 구성

`.magento.app.yaml`에서 응용 프로그램과 Valkey 서비스 간의 관계를 구성합니다.

```yaml
relationships:
  valkey: "cache:valkey"
```

관계 키 `valkey`은(는) 응용 프로그램에서 서비스에 액세스하는 데 사용하는 이름입니다. 값 `cache:valkey`은(는) `.magento/services.yaml`에 정의된 서비스 ID 및 서비스 형식을 참조합니다.

>[!TIP]
>
>Adobe Commerce은 기본적으로 일반 PHP 소켓에서 작동하는 `credis` 클라이언트 라이브러리를 통해 Valkey와 통신합니다. 성능을 향상시키려면 `.magento.app.yaml`에서 `redis` PHP 확장을 사용하도록 설정하십시오. `credis`은(는) 사용 가능한 경우 컴파일된 확장을 자동으로 사용합니다.
>
>```yaml
>runtime:
>   extensions:
>       - redis
>```

### 변경 사항 커밋 및 배포

구성 변경 사항을 추가, 커밋 및 푸시합니다.

```terminal
git add .magento/services.yaml .magento.app.yaml
git commit -m "Enable Valkey service"
git push origin <branch-name>
```

배포가 완료되면 Valkey 서비스 관계를 사용할 수 있는지 확인합니다.

{{service-change-tip}}

{{valkey-newrelic}}

## Valkey 구성 사용자 지정

캐시, 세션, L2 및 복제본 연결 권장 사항에 대해서는 _구현 플레이북 모범 사례 안내서_&#x200B;에서 [Valkey 및 Redis 서비스 구성에 대한 모범 사례](https://experienceleague.adobe.com/ko/docs/commerce-operations/implementation-playbook/best-practices/planning/redis-valkey-service-configuration)를 참조하십시오.

## 서비스 관계 확인

디코딩한 `MAGENTO_CLOUD_RELATIONSHIPS` 개체를 표시하려면 구성을 배포한 후 응용 프로그램 컨테이너에서 다음 명령을 실행하십시오.

SSH를 사용하여 원격 클라우드 환경에 연결한 다음 다음을 실행합니다.

```terminal
echo "$MAGENTO_CLOUD_RELATIONSHIPS" | base64 -d | json_pp
```

이 명령은 구성된 모든 서비스 관계를 표시합니다. Valkey 연결 세부 사항을 식별하려면 Valkey 관계를 찾습니다.

**출력 예**

다음 약식 예제는 `valkey` 관계를 보여 줍니다. 유니버설 스키마가 아닙니다.

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
   "valkey" : [
      {
         "host" : "valkey.internal",
         "port" : 6379,
         "path" : null,
         "scheme" : "valkey"
      }
   ]
}
```

출력은 환경 및 서비스 구성에 따라 달라집니다. 이 예제에서 호스트 이름, 포트, IP 주소, 클러스터 이름, 서비스 버전, 사용자 이름 또는 암호를 하드 코딩하지 마십시오. 대상 환경에서 `MAGENTO_CLOUD_RELATIONSHIPS`이(가) 반환한 값을 사용합니다.

`jq`을(를) 사용할 수 있는 경우 Valkey 관계만 표시합니다.

```terminal
printf '%s' "$MAGENTO_CLOUD_RELATIONSHIPS" \
  | base64 -d \
  | jq '{valkey: .valkey}'
```

서비스 관계에 대한 자세한 내용은 [서비스 구성](services-yaml.md)을 참조하세요.

## Valkey CLI 사용

Valkey 관계 이름이 `valkey`이라고 가정할 경우 `MAGENTO_CLOUD_RELATIONSHIPS`에서 반환된 호스트 및 포트를 사용하여 Valkey에 연결하십시오.

```terminal
valkey-cli -h <host> -p <port>
```

**예**

```terminal
valkey-cli -h valkey.internal -p 6379
```

## 설치된 Valkey 버전 가져오기

>[!BEGINTABS]

>[!TAB 통합 환경]

통합 환경에서 `valkey` 관계에서 반환된 호스트 및 포트를 사용하여 다음을 실행합니다.

```terminal
valkey-cli -h <host> -p <port> info | grep version
```

**응답 예**

```text
valkey_version:<installed-version>
gcc_version:<gcc-version>
```

버전 및 빌드 세부 사항은 환경에 따라 다릅니다. 표시된 예제 버전을 필수 또는 범용 서비스 버전으로 취급하지 마십시오.

>[!TAB Pro 스테이징 및 프로덕션]

Pro 스테이징 및 프로덕션 환경에서 다음을 실행합니다.

```terminal
valkey-server -v
```

**응답 예**

```text
Valkey server v=<installed-version> ...
```

버전 및 빌드 세부 사항은 환경에 따라 다릅니다. 표시된 예제 버전을 필수 또는 범용 서비스 버전으로 취급하지 마십시오.

>[!ENDTABS]

## 문제 해결 확인

### Valkey가 구성한 캐시에 대한 캐시 정리 오류 참조 Redis

`cache` 서비스가 Valkey로 구성된 경우에도 배포 전 캐시 정리 실패가 오류 코드 `[107]`(`clean-redis-cache`)과(와) `Connection to Redis` 메시지를 표시할 수 있습니다. `ece-tools`은(는) 지원 캐시 서비스가 Redis인지 Valkey인지에 관계없이 캐시 정리 단계에 이 오류 코드와 메시지를 사용합니다.

기본 오류가 관계 호스트의 `Name or service not known`과 같은 DNS 오류인 경우 서비스 관계를 사용하기 전에 배포 단계가 실행되었거나 `.magento.app.yaml`의 관계 이름이 `.magento/services.yaml`의 서비스 ID와 일치하지 않습니다. [서비스 관계 확인](#verify-the-service-relationship)을 참조하세요.
