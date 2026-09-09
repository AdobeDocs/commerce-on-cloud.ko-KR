---
title: 앱 이해
description: Adobe Commerce Traffic Insights의 작동 방식, 필터로 전환하는 방법, 데이터를 측정하는 방법, 데이터 제한 사항 및 성능에 대해 알아봅니다.
feature: Cloud, Observability
role: Admin
source-git-commit: 09318645dd341a74d72237f4d4162d1d26d03651
workflow-type: tm+mt
source-wordcount: '949'
ht-degree: 0%

---

# 앱 이해

[!DNL Adobe Commerce Traffic Insights] 앱은 원시 CDN(Fastly Content Delivery Network) 액세스 로그를 저장소 에지 트래픽의 그림으로 시각화합니다. 차트는 다음 탭으로 그룹화됩니다.

- **대역폭** — 트래픽 대역폭이 도메인, 콘텐츠 및 리소스 유형, 클라우드 프로젝트 간에 시간이 지남에 따라 분산되는 방식.
- **전체 페이지 캐시 성능** - PDP(제품 세부 사항 페이지), PLP(제품 목록 페이지) 및 CMS(컨텐츠 관리 시스템) 페이지를 위한 HTML이 에지에서 캐시되는 동적 저장 공간 성능을 얼마나 효율적으로 사용할 수 있습니까?
- **봇 활동 및 분석 요청** — 알려진 봇 에이전트, 지리적 위치, IP/서브넷, URL 및 Fastly Next-Gen Web Application Firewall(WAF) 신호로 분류된 트래픽.

네 번째 인앱 **설명서** 탭에는 개념 노트와 [조사 플레이북](investigation-playbook.md)이 들어 있습니다.

## 이 가이드는 누구의 것인가요?

- CDN 대역폭 초과, 트래픽 스파이크 또는 원본 로드를 조사하는 **사이트 운영자 및 SRE(Site Reliability Engineering)**.
- **개발자** FPC(전체 페이지 캐시) 범위 및 적중률을 조정하거나 VCL(Fastly Varnish Configuration Language) 규칙을 구현합니다.
- **관리자 및 보안 엔지니어** 원치 않는 보트, 스크레이퍼 및 악의적인 자동 트래픽을 식별하고 완화합니다.

[!DNL Adobe Commerce on Cloud Infrastructure], Fastly CDN 개념 및 기본 New Relic 탐색에 익숙하다고 가정합니다.

## 작동 방식

페이지 상단의 플랫폼 컨트롤에서 계정 및 시간 범위를 선택합니다. 선택적 **프로젝트 ID**&#x200B;을(를) 사용하면 차트 범위를 특정 클라우드 프로젝트로 좁힐 수 있습니다. 마스터 계정 또는 파트너 관계 설정에서 드롭다운에서 계정을 볼 수 있다고 해서 계정을 쿼리할 수 있는 것은 아닙니다. 차트에서 권한 오류를 보고하면 NRQL(New Relic 쿼리 언어) 액세스 권한이 있는 계정으로 전환합니다.

필터를 계속 적용하여 광범위한 개요를 집중 조사로 전환합니다. 보트, IP, 서브넷, 국가 또는 콘텐츠 형식과 같은 패싯 열의 값을 클릭하여 [전역 필터](https://docs.newrelic.com/docs/query-your-data/explore-query-data/dashboards/filter-new-relic-one-dashboards-facets/#example-use)를 추가합니다. 활성 필터는 그리드 맨 위에 표시되며 모든 탭의 모든 위젯에 동시에 적용됩니다. 범위를 넓히려면 필터를 제거하십시오.

**연습** - *총 대역폭*&#x200B;이 계약 허용 범위를 초과하는 추세를 보이고 있으며 이를 실행하는 사람이 누구인지 알고 싶은 시나리오를 생각해 보십시오.

1. **보트 활동 및 요청 분석** 탭을 열고 **대역폭 구조**&#x200B;를 읽어 자동 트래픽과 자동 트래픽의 양을 확인합니다.
1. 보트에 트래픽이 더 많은 것 같으면 **대역폭별 알려진 보트**&#x200B;를 열고 가장 무거운 봇(예: 스크레이퍼)을 클릭합니다. 이렇게 하면 새 필터가 추가되므로 이제 모든 위젯의 범위가 해당 봇에 지정됩니다.
1. 요청 속도, 상태 혼합 및 FPC 적중률에 대해 **알려진 봇 영향 세부 정보**&#x200B;를 읽으십시오.
1. 보트의 원본 위치를 보려면 **국가별 대역폭**&#x200B;을 확인하세요. 봇이 가져오는 내용을 보려면 대역폭별 **URL**&#x200B;을(를) 참조하십시오.
1. 트래픽이 한 네트워크에 집중되는 경우 **IP 서브넷별 통계**&#x200B;를 클릭하여 한 블록의 주소 간에 회전하는 행위자를 확인하십시오.
1. 이제 대상 완화 작업을 작성하는 데 필요한 사람, 내용 및 위치가 있습니다. 진행 방법에 대해 알아보려면 [조사 플레이북](investigation-playbook.md)을 계속하십시오.

동일한 필터링 방법은 의심스러운 국가, 단일 IP, 콘텐츠 유형 또는 URL 경로 세그먼트 등 모든 시작 측면에서 작동합니다.

## 데이터 측정 방법

몇 가지 측정 선택 사항을 이해하면 숫자를 보다 쉽게 신뢰하고 해석할 수 있습니다.

- **Bandwidth(BW)**&#x200B;은 CDN이 일치하는 요청에 대해 제공한 총 바이트로, **응답 헤더와 본문 모두 계산**&#x200B;합니다. 그것은 계약 수당에 대해 계산되는 헤드라인 비용 지표이다.
- **요청(필수)** 은(는) 고유 요청의 수입니다. 단, Fastly [shielding](https://www.fastly.com/documentation/guides/concepts/shielding/)을(를) 사용하면 단일 요청이 다음 각 항목에 대해 한 번씩 **두 번**&#x200B;기록됩니다.
  - 내부 실드 [POP(Point of Presence)](https://www.fastly.com/documentation/guides/getting-started/concepts/using-fastlys-global-pop-network/)
  - EDGE POP
    응답이 로컬 POP 캐시에서 직접 나오거나 실드 자체가 발신자 위치의 POP 역할을 하는 경우가 아니면 이 문제가 발생합니다. 이러한 `HIT,MISS` 및 `MISS,MISS` 사례를 두 번 계산하지 않도록 하기 위해 앱의 쿼리는 `request_id` 필드에서 [`uniqueCount`](https://docs.newrelic.com/docs/nrql/nrql-syntax-clauses-functions/#func-uniqueCount)과(와) 함께 집계됩니다. 이 경우 정확한 수가 아닌 예상 오차 범위가 **~5%**&#x200B;인 가까운 **approximation**&#x200B;이 반환됩니다.
- **CDN 네트워크 세그먼트**&#x200B;는 다르게 압축됩니다. 클라이언트에 전달된 응답이 압축되어 있지만 Shield-to-POP 트래픽은 [ESI(Edge Side Includes)](https://www.fastly.com/documentation/reference/vcl/statements/esi/) 지원을 유지하기 위해 [압축되지 않음](https://www.fastly.com/documentation/guides/concepts/compression/#compression-at-the-edge)입니다. 따라서 캐시 적중률이 낮으면 캐시되지 않은 콘텐츠를 전체, 압축되지 않은 크기로 반복적으로 쉴드에서 가져와야 하므로 클라이언트 쪽 세그먼트보다 내부 세그먼트가 더 늘어납니다. 이 압축으로 인해 **CDN 네트워크 세그먼트 대역폭** 위젯과 FPC 적중률이 동일한 기본 비용의 두 보기입니다.

## 데이터 제한 사항 및 성능

- **30일 유지** - Fastly CDN 로그는 구독 플랜에 따라 **30일** 동안 New Relic에 유지됩니다. 선택하는 모든 창은 지난 30일 이내에 있어야 합니다. 장기 **total** 대역폭의 경우 [!DNL Adobe Commerce admin] 패널, **대시보드 > Fastly > 대역폭 > 총**&#x200B;에서 직접 Fastly 통합을 사용하되, 서비스 ID별로 보고하므로 환경별로 데이터를 수집하고 집계하여 계약 허용과 비교해야 합니다.
- **60초 쿼리 제한** - 각 차트의 NRQL에는 [60초 실행 제한](https://docs.newrelic.com/docs/nrql/using-nrql/rate-limits-nrql-queries/#query-duration)이 있습니다. 트래픽이 많은 계정의 경우 너무 많은 로그 레코드를 검색하는 동안 위젯이 시간 초과될 수 있습니다. 이 경우 시간 범위를 줄이고 차트를 다시 로드합니다. 더 밝은 탭을 위해 다시 확장할 수 있습니다.
