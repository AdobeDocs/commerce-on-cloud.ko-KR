---
title: 정적 파일에 대한 캐시 설정
description: ' [!DNL Commerce] 응용 프로그램 구성 파일에서 캐시 저장소 옵션을 설정하는 방법에 대해 알아봅니다.'
feature: Cloud, Configuration, Cache, SCD
exl-id: 0f577974-85d7-4972-8f03-856aa6accaae
TQID: https://experienceleague.adobe.com/ZA0WRB9p4Gpi7kjWxNPS5uCSfaTmrkrqxc4SgC9y9og
product_v2:
  - id: eadea719-cf89-469b-a6fd-a236a7138047
feature_v2:
  - id: dac87252-6066-4d6e-a9d2-f6d84c323de7
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
source-git-commit: fd3ef8201c368f889344452e334976070a6c7157
workflow-type: tm+mt
source-wordcount: 131
ht-degree: 0%

---

# 정적 파일에 대한 캐시 설정

미디어 및 정적 파일의 캐시 TTL(time-to-live)이 `expires` 키를 사용하여 `.magento.app.yaml` 구성 파일에 설정되어 있습니다.

>[!NOTE]
>
>프로덕션 환경을 업데이트하기 전에 스테이징 환경에서 변경 사항을 테스트하는 것이 중요합니다. 이러한 환경에서 구성을 업데이트하는 데 도움이 필요하면 [Adobe Commerce 지원 티켓을 제출](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket)하십시오.

1. `.magento.app.yaml` 파일의 [`web` 속성](web-property.md)에 TTL 시간(초)을 지정하십시오. `locations` 또는 `"/media"` 및 `"/static"`에서 `expires` 키를 추가할 수 있습니다.

   캐시가 만료되지 않도록 하려면 `expires: -1` 키-값 쌍을 사용하십시오. 다음 예를 참조하십시오.

   ```yaml
   # The configuration of app when it is exposed to the web.
   web:
     locations:
       "/media":
         ...
         expires: -1
   
       "/static":
         ...
         expires: -1
   ```

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add -A && git commit -m "Set cache TTL for static files" && git push origin <branch-name>
   ```
