---
title: CMS 백엔드로 요청 재라우팅
description: Fastly Edge 모듈을 사용하여 Adobe Commerce 스토어의 수신 요청을 별도의 WordPress 사이트로 라우팅하는 방법에 대해 알아봅니다.
feature: Cloud, Configuration, Routes
source-git-commit: 1e789247c12009908eabb6039d951acbdfcc9263
workflow-type: tm+mt
source-wordcount: '307'
ht-degree: 0%

---

# CMS 백엔드로 요청 재라우팅

Fastly Edge Module _Edge 사전을 사용한 기타 CMS/백엔드 통합_&#x200B;을 사용하여 Adobe Commerce 스토어에서 들어오는 요청을 별도의 WordPress 사이트로 다시 라우팅합니다. 유사한 프로세스에 따라 요청을 다른 CMS 백엔드로 다시 라우팅할 수 있습니다.

VCL 코드를 수동으로 작성하고 Fastly API를 사용하여 업로드하는 대신, Fastly Edge 모듈 을 사용하여 관리자에서 사용자 지정 VCL 코드를 만들고 업로드합니다.

>[!NOTE]
>
>프로덕션 환경에서 Fastly 서비스 구성을 업데이트하기 전에 사용자 지정 VCL 구성을 테스트할 수 있는 스테이징 환경에 추가하는 것이 좋습니다.

**필수 구성 요소**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

**Adobe Commerce에서 WordPress로 요청을 다시 라우팅하려면**:

1. 스테이징 또는 프로덕션 환경에서 Fastly Edge 모듈을 활성화합니다.

   - 관리자에 로그인합니다.

   - **스토어** > 설정 > **구성** > **고급** > **시스템** > **전체 페이지 캐시** > **빠른 구성** > **고급 구성**&#x200B;으로 이동합니다.

   - **Fastly Edge 모듈**&#x200B;의 값을 **예**(으)로 설정합니다.

   - 구성을 저장합니다.

1. WordPress 백엔드로 라우팅할 URL 경로를 식별합니다.

1. 다음 작업을 완료하여 Fastly 서비스를 구성하고 WordPress 백엔드에 대한 요청 경로 재지정을 위한 사용자 정의 VCL 코드를 만듭니다.

   - Adobe Commerce 저장소에서 백엔드로 라우팅할 경로를 지정하는 Edge 사전을 만듭니다.

   - Fastly 서비스 구성에 WordPress 백엔드를 추가하고 URL 재작성에 대한 요청 조건을 첨부합니다.

   - Adobe Commerce에서 WordPress 백엔드로 URL 재작성을 처리하도록 _기타 CMS/백 엔드 통합_ Edge 모듈을 구성합니다.

     자세한 지침은 _Fastly CDN module for Target 2_ 설명서의 [Fastly Edge Modules - Other CMS Magento/Backend 통합](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/Edge-Modules/EDGE-MODULE-OTHER-CMS-INTEGRATION.md)을 참조하십시오.

1. Fastly 서비스 구성을 업데이트한 후 Adobe Commerce 스토어를 테스트하여 WordPress에 대해 지정된 URL 요청이 올바르게 리디렉션되는지 확인하십시오.
