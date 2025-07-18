---
title: Valkey 서비스 설정
description: Cloud Infrastructure의 Adobe Commerce에 대한 백엔드 캐시 솔루션인 Valkey를 설정하고 최적화하는 방법에 대해 알아봅니다.
feature: Cloud, Cache, Services
exl-id: f8933e0d-a308-4c75-8547-cb26ab6df947
source-git-commit: cf2e659267445603b3f5eaf877f4eb7ac0c1b54c
workflow-type: tm+mt
source-wordcount: '201'
ht-degree: 0%

---

# Valkey 서비스 설정

[Valkey](https://valkey.io)은(는) Adobe Commerce에서 기본적으로 사용하는 `Zend Framework Zend_Cache_Backend_File`을(를) 대체하는 선택적 백엔드 캐시 솔루션입니다.

[구성 가이드](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/valkey/config-valkey.html){target="_blank"}에서 _Valkey 구성_&#x200B;을(를) 참조하십시오.

{{service-instruction}}

**Redis를 Valkey로 바꾸려면 다음 세 개의 파일에서 구성을 업데이트하세요**:

1. `.magento/services.yaml` 파일에 필요한 이름과 형식을 추가합니다.

   ```yaml
   cache:
       type: valkey:<version>
   ```

   고유한 Valkey 구성을 제공하려면 `core_config` 파일에 `.magento/services.yaml` 키를 추가하십시오.

   ```yaml
   cache:
       type: valkey:8.0
   ```

1. `.magento.app.yaml` 파일에서 관계를 구성합니다.

   ```yaml
   relationships:
       valkey: "cache:valkey"
   ```

1. 다음과 같이 `.magento.env.yaml`을(를) 구성합니다.

   ```yaml
    stage:
        deploy:
        VALKEY_USE_SLAVE_CONNECTION: true
        VALKEY_BACKEND: '\Magento\Framework\Cache\Backend\RemoteSynchronizedCache'
   ```

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add .magento/services.yaml .magento.app.yaml .magento.env.yaml && git commit -m "Enable valkey service" && git push origin <branch-name>
   ```

1. [서비스 관계를 확인](services-yaml.md#service-relationships)합니다.

{{service-change-tip}}

## Valkey CLI 사용

Valkey 관계의 이름이 `valkey`이라고 가정하면 `valkey-cli` 도구를 사용하여 액세스할 수 있습니다.

1. SSH를 사용하여 Valkey가 설치 및 구성된 통합 환경에 연결합니다.

1. 호스트에 대한 SSH 터널을 엽니다.

   ```bash
   valkey-cli -h valkey.internal
   ```

## 설치된 Valkey 버전 가져오기

다음 명령을 사용하여 통합 환경에 설치된 Valkey 버전을 가져옵니다.

```bash
valkey-cli -h valkey.internal info | grep version
```

응답:

```
valkey_version:8.0.1
gcc_version:12.2.0
```

### Pro 스테이징 및 프로덕션에 대한 유효성 검사

스테이징 또는 프로덕션 환경에 설치된 Valkey 버전을 가져오려면 `valkey-server` 명령을 사용합니다.

```bash
valkey-server -v
```

```bash
Valkey server v=8.0.1 ...
```

다음 명령을 사용하여 Pro 스테이징 또는 프로덕션 환경에 Valkey 구성을 설치합니다.

```bash
echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
```

응답:

```json
"valkey" : [
    {
        "cluster" : "project-master-abc0003",
        "epoch" : 0,
        "fragment" : null,
        "host" : "valkeycache.internal",
        "host_mapped" : false,
        "hostname" : "oblahblahblahblahe.cache.service._.magentosite.cloud",
        "instance_ips" : [
        "123.456.789.012"
        ],
        "ip" : "123.456.789.012",
        "password" : null,
        "path" : null,
        "port" : 6379,
        "public" : false,
        "query" : {},
        "rel" : "valkey",
        "scheme" : "valkey",
        "service" : "cache",
        "type" : "valkey:8.0",
        "username" : null
    }
]
```
