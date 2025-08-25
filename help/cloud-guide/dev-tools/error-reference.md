---
title: ECE-Tools 패키지에 대한 오류 메시지
description: 클라우드 인프라 빌드, 배포 및 배포 후 프로세스에서 Adobe Commerce을 수행하는 동안 발생할 수 있는 오류 코드 및 메시지 목록을 참조하십시오.
recommendations: noDisplay
role: Developer
exl-id: e240c268-b171-44e9-9fa4-f0e91b0d9899
source-git-commit: d08ef7d46e3b94ae54ee99aa63de1b267f4e94a0
workflow-type: tm+mt
source-wordcount: '243'
ht-degree: 0%

---

# ECE-Tools용 오류 메시지

이 오류 메시지 참조는 클라우드 인프라 빌드, 배포 및 배포 후 프로세스에서 Adobe Commerce을 수행하는 동안 발생할 수 있는 오류를 해결하기 위한 정보를 제공합니다.

배포 중에 발생하는 모든 중요 및 경고 오류 메시지는 `var/log/cloud.log` 및 `/var/log/cloud.error.log` 파일에 모두 기록됩니다. 클라우드 오류 로그 파일에는 최신 배포의 오류만 포함되어 있습니다. 빈 파일은 오류 없이 성공적으로 배포했음을 나타냅니다.

`cloud.error.log` 파일에서 각 항목은 보다 쉽게 구문 분석할 수 있도록 JSON 문자열로 포맷됩니다.

```json
{"errorCode":1006,"stage":"build","step":"validate-config","suggestion":"No stores/website/locales found in config.php\n  To speed up the deploy process do the following:\n  1. Using SSH, log in to your Magento Cloud account\n  2. Run \"php ./vendor/bin/ece-tools config:dump\"\n  3. Using SCP, copy the app/etc/config.php file to your local repository\n  4. Add, commit, and push your changes to the app/etc/config.php file","title":"The configured state is not ideal","type":"warning"}
```

오류 메시지는 배포 단계인 빌드, 배포 및 사후 배포 중 하나로 분류됩니다. 각 섹션은 각 오류에 대한 다음 정보와 함께 관련 오류 목록을 제공합니다.

- **오류 코드**: 오류 메시지에 대한 Adobe Commerce 할당 식별자
- **Stage**: 빌드, 배포 또는 배포 후 단계 중 오류가 발생했는지 여부를 나타냅니다.
- **단계**: 배포 시나리오에서 오류를 반환할 수 있는 단계를 나타냅니다. _단계_ 열이 비어 있으면 오류는 여러 단계 또는 사전 처리 작업 중에 반환될 수 있는 일반적인 오류입니다. 빌드, 배포 및 배포 후 단계에 대한 자세한 내용은 [시나리오 기반 배포](../deploy/scenario-based.md)를 참조하십시오.
- **제안**: 문제를 해결하고 오류를 해결하기 위한 지침을 제공합니다.
- **제목(오류 설명)**: 오류의 원인을 요약하는 설명입니다
- **Type**: 오류가 심각한 오류인지 또는 경고인지를 나타냅니다.

{{$include /help/_includes/automated/ece-tools-error-codes.md}}

<!-- Last updated from includes: 2025-05-28 21:01:41 -->
