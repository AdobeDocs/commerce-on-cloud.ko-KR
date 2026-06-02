---
title: 데이터 수집
description: New Relic에서 Commerce 데이터 수집을 보고 관리하는 방법을 알아봅니다.
feature: Cloud, Observability
exl-id: b457b4de-deeb-4e92-b95a-c2b89d6f7a05
TQID: https://experienceleague.adobe.com/60hhI0IvazUSrw6cCRoXfbGjA4fkLLPXb8Q4Q08AqeE
product_v2: id: eadea719-cf89-469b-a6fd-a236a7138047
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bdid: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2: id: ebde5b41-29c9-4f5e-9ef6-1197e85409e3id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: fd3ef8201c368f889344452e334976070a6c7157
workflow-type: tm+mt
source-wordcount: 209
ht-degree: 0%

---

# 데이터 수집

New Relic은 효과적인 모니터링 및 분석을 제공하기 위해 풍부한 데이터에 의존하지만, 대규모 데이터 세트는 시기 적절한 결과, 성능 및 규정 준수에 영향을 줄 수 있습니다. 이 항목에서는 데이터 수집 관리에 대한 몇 가지 지침과 데이터를 가장 효과적으로 미세 조정하는 전략을 제공합니다.

New Relic에서는 데이터 소스별 플랜 사용량을 요약하는 _데이터 관리_ 보기를 제공합니다.

**수집 데이터 및 소스를 보려면**:

1. New Relic 사용자 메뉴에서 **[!UICONTROL Manage your data]**&#x200B;을(를) 클릭합니다.
1. _관리_ 목록에서 **[!UICONTROL Data management]**&#x200B;을(를) 클릭합니다.

   ![데이터 관리](../../assets/new-relic/data-ingestion.png)

   **[!UICONTROL Data ingestion]** 탭에는 해당 날짜에 대해 수집된 데이터와 데이터 원본이 표시됩니다.
데이터 보존 탭에는 데이터가 저장되는 기간이 표시되고 제어됩니다.

1. **[!UICONTROL Limits]** 탭을 선택하고 계정에 대한 제한을 확인합니다.

Adobe Commerce의 데이터 소스는 다음과 같습니다.

- **APM 이벤트** - 차트 및 대시보드에 사용된 이벤트 데이터
- **인프라**—CPU, 스토리지, 네트워킹과 같은 프로세스 및 호스트 지표
- **로깅**—CDN, APM 및 응용 프로그램 서버에 대한 로그

로그 데이터는 수집의 많은 부분에 기여합니다. [로그 데이터를 보고 분석](log-management.md#view-and-analyze-log-data)하는 방법과 Adobe 담당자와 협력하여 데이터 수집 및 유지 요구 사항에 대한 전략을 구성하는 방법을 알아보십시오. _New Relic 설명서_&#x200B;에서 [데이터 수집 관리](https://docs.newrelic.com/docs/data-apis/manage-data/manage-data-coming-new-relic/)에 대해 자세히 알아보세요.
