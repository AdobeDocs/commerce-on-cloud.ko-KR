---
title: Adobe Commerce 트래픽 인사이트
description: Adobe Commerce 트래픽 인사이트 도구에 대해 알아보고, Adobe Commerce on cloud infrastructure 프로젝트의 트래픽을 이해하는 데 어떻게 도움이 되는지에 대해 알아봅니다.
feature: Cloud, Observability
role: Admin
source-git-commit: 119c9415abd22221e3ae785445d537f0609eba14
workflow-type: tm+mt
source-wordcount: '443'
ht-degree: 0%

---

# 트래픽 인사이트

Adobe Commerce Traffic Insights는 [!DNL Adobe Commerce on Cloud Infrastructure] Fastly CDN 트래픽을 시각화하는 New Relic One 앱입니다. 이미 `Log` 이벤트로 New Relic에 전달되는 Fastly CDN 액세스 로그 줄을 읽고 선택한 New Relic 계정 및 플랫폼 시간 범위로 범위가 지정된 조정된 조정된 차트 집합을 렌더링합니다. 이렇게 하면 New Relic의 쿼리 언어인 NRQL을 직접 작성하지 않고도 스토어의 에지 트래픽을 시각화할 수 있습니다.

## 조사에 도움이 되는 사항

트래픽 인사이트는 다음 세 가지 일반적인 문제를 해결하는 데 도움이 되도록 설계되었습니다.

- **CDN 대역폭 초과** — 계약 허용치를 초과하는 트래픽 트렌드입니다. 볼륨을 특정 도메인, 콘텐츠 유형, URL 또는 프로젝트까지 무거운 미디어, 큰 파일, 캐시할 수 없는 404 페이지 또는 비효율적인 캐시에 연결합니다.
- **검색 보트와 웹 크롤러 로드** — 요청의 불균형적인 공유를 생성하여 캐시 효율성과 원본 로드를 손상시키는 검색 엔진 또는 AI 웹 크롤러. 이름이 지정된 봇이 가장 활동적이고 정확히 무엇을 가져오는지 확인합니다.
- **악성 스크립트 및 스크레이퍼** — 스크래핑, 자격 증명 스터핑, 카드 테스트, 가짜 계정 만들기 또는 레이어 7 남용. Fastly Next-Gen WAF 신호 및 의심스러운 트래픽 배후의 IP, 서브넷 및 국가를 표시합니다.

각 경우에 앱은 트래픽의 *사람, 내용, 위치*&#x200B;를 식별합니다. Commerce 및 Fastly 구성에서 Fastly VCL 규칙, 이미지 최적화, 캐시 튜닝, 속도 제한 또는 Adobe의 [고급 보안](../../cdn/advanced-security.md) 추가 기능을 통해 이러한 정보에 작용합니다. [Investigation 플레이북](investigation-playbook.md)에서는 이러한 각 문제를 다룹니다.

## 앱 액세스

- **직접 연결:** [Adobe Commerce 트래픽 인사이트](https://one.newrelic.com/a9a0c3b8-3844-4ca1-8bad-c6742747be47).
- **New Relic One 홈 화면**(one.newrelic.com) — 계정이 앱을 구독하면 홈 페이지에 고유한 타일 **Adobe Commerce 트래픽 인사이트**&#x200B;로 표시됩니다.
- **상단 검색 창(빠른 찾기)에서** — `Adobe Commerce Traffic Insights`을(를) 검색하고 결과에서 선택하십시오.
- **더 빠른 액세스를 위해 고정하려면** - 앱의 타일 또는 페이지 머리글에서 별 또는 고정 컨트롤을 사용하여 즐겨찾기 또는 왼쪽 탐색에 추가하십시오. 이 컨트롤의 정확한 위치는 계정에 사용 중인 New Relic UI 버전에 따라 다릅니다.

## 이 안내서에서

- **[앱 이해](understanding-the-app.md)** - 트래픽 인사이트, 필터로 앱 실행 방법, 숫자 측정 방법, 데이터가 알려줄 수 있는 것과 알려줄 수 없는 것.
- **[조사 플레이북](investigation-playbook.md)** - 앱이 빌드되어 대역폭 초과, 웹 크롤러 로드 및 악의적인 트래픽의 세 가지 문제를 해결하는 데 권장되는 방법입니다. 이러한 각 차트는 해당 차트를 확인하고 수동 완화가 충분하지 않은 경우에 대한 Adobe의 기본 [고급 보안](../../cdn/advanced-security.md) 에스컬레이션 경로를 지정합니다.