---
source-git-commit: 0df07e865c3c4fc4ac14483972643eafa8814726
workflow-type: tm+mt
source-wordcount: '112'
ht-degree: 0%

---
# Fastly에 대한 사용자 지정 VCL을 수정하기 위한 파일 포함

## 사용자 지정 VCL 코드 조각 수정

1. 책임자에 [로그인](/help/get-started/onboarding.md#access-your-admin-panel).

1. **스토어** > **설정** > **구성** > **고급** > **시스템**&#x200B;을 클릭합니다.

1. **전체 페이지 캐시** > **빠른 구성** > **사용자 지정 VCL 조각**&#x200B;을 확장합니다.

   ![사용자 지정 VCL 코드 조각 관리](/help/assets/cdn/fastly-manage-snippets.png)

1. _Action_ 열에서 편집할 코드 조각 옆에 있는 설정 아이콘을 클릭합니다.

1. 페이지가 다시 로드되면 _Fastly 구성_ 섹션에서 **Fastly에 VCL 업로드**&#x200B;를 클릭합니다.

1. 업로드가 완료되면 페이지 상단의 알림에 따라 캐시를 새로 고칩니다.

>[!WARNING]
>
>_사용자 지정 VCL 코드 조각_ UI 옵션은 Adobe Commerce 관리자를 통해 추가된 코드 조각만 표시합니다. Fastly API를 사용하여 코드 조각을 추가하는 경우 API를 사용하여 [관리하세요](/help/cloud-guide/cdn/fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api).
