---
title: 조사 플레이북
description: Adobe Commerce 트래픽 인사이트를 사용하여 CDN 대역폭 초과, 검색 봇 및 웹 크롤러 로드, 악의적인 트래픽을 조사하는 방법과 에스컬레이션 시기를 알아봅니다.
feature: Cloud, Observability
role: Admin
source-git-commit: 09318645dd341a74d72237f4d4162d1d26d03651
workflow-type: tm+mt
source-wordcount: '1774'
ht-degree: 0%

---

# 조사 플레이북

[!DNL Adobe Commerce Traffic Insights] 앱은 다음 문제를 조사하는 데 도움이 되도록 설계되었습니다.

- 대역폭 초과
- 웹 크롤러 로드
- 악의적인 트래픽

또는 수동 완화가 충분하지 않은 경우 Adobe의 기본 에스컬레이션 경로인 [고급 보안: 기본 보트 관리, L7 DDoS 및 속도 제한](#advanced-security-native-bot-management-layer-7-ddos--rate-limiting)을(를) 요청할 수 있습니다. 각 단계는 증상을 나타내는 위젯을 참조하므로 지표에서 구체적인 동작으로 이동할 수 있습니다.

>[!WARNING]
>
>이 페이지에 대한 제안은 지침일 뿐입니다. 배포 전에 항상 자체 트래픽에 대해 차단 규칙을 확인합니다.

## CDN 대역폭 초과

대역폭 초과를 고려하기 전에 대역폭 청구에 대해 이해합니다. 모든 프로덕션 **및** 스테이징 환경을 포함하여 [!DNL Adobe Commerce on Cloud Infrastructure] 계정과 함께 번들로 제공되는 **모두** Fastly 서비스의 트래픽은 계약의 연간 허용량과 비교하여 일반적인 사용량에 포함됩니다. **대역폭 > 총 대역폭**&#x200B;부터 시작한 다음 볼륨을 콘텐츠 유형별 **대역폭** 및 도메인별 **대역폭**&#x200B;으로 지정합니다.

### 미디어 콘텐츠

일부 매장에서는 카탈로그로 인해 합법적으로 대량의 대역폭을 미디어로서 제공하고 있습니다. **콘텐츠 유형별 대역폭**&#x200B;에 상당한 양의 미디어 대역폭이 표시되면 다음 완화 사항을 고려하십시오.

- 더 작고 낮은 품질의 이미지를 제공하기 위해 [Fastly 손실 변환](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/fastly-image-optimization#force-lossy-conversion)을 실험해 보십시오.
- [Fastly Deep 이미지 최적화](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/fastly-image-optimization#deep-image-optimization)를 조사하여 CDN(Content Delivery Network) 측에서 크기 조정된 이미지를 생성합니다.

### 큰 파일

일부 사이트에는 ERP(Enterprise Resource Planning) 통합 또는 내보내기와 같은 대규모 파일 또는 특정 대량 응답이 포함되어 있습니다. **대역폭별 URL**&#x200B;을(를) 사용하여 **BW** 및 **평균 크기** 열을 검토하여 이러한 대용량 파일을 찾으십시오. 더 높은 수준의 보기에서는 **대역폭별 경로 세그먼트 수준 1**&#x200B;을 사용할 수 있습니다.

### 헤비

Adobe Commerce **404 페이지를 찾을 수 없음**&#x200B;은(는) 대개 무거운 테마 스타일의 페이지(~1.5MB)이고 **캐싱할 수 없음**&#x200B;이므로 404를 반복하면 비정상적인 트래픽이 발생할 수 있습니다. `favicon.ico`과(와) 같은 사소한 누락된 리소스도 작은 파일이 아닌 무거운 `404` 페이지로 바뀔 수 있습니다. **도메인별 대역폭**, **대역폭별 URL**, **대역폭별 상위 IP** 및 **IP 서브넷별 통계**&#x200B;의 **404** 및 **404 BW** 열을 사용하여 404 볼륨을 지속적으로 생성하는 클라이언트, IP 및 URL을 찾으십시오. 그런 다음 해당 액세스를 줄이거나 제한하십시오. 예를 들어 간단한 `403`을(를) 대신 반환합니다.

### 낮은 FPC 적중률

[!DNL Adobe]은(는) 기본 CDN 캐시 집계가 원본을 제공하도록 Fastly [shielding](https://www.fastly.com/documentation/guides/concepts/shielding/)을(를) 사용하도록 설정하는 것이 좋습니다. 따라서 클라이언트와 가장 가까운 로컬 Points of Presence([POPs](https://www.fastly.com/documentation/guides/getting-started/concepts/using-fastlys-global-pop-network/))에서 원본 캐시에 도달하는 요청이 줄어듭니다. [구성 확인](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/setup-fastly/fastly-custom-cache-configuration#configure-back-ends-and-origin-shielding)을 참조하세요.

POP-to-Client 및 Shield-to-POP 트래픽은 별도로 계산되며, 클라이언트 응답이 압축되는 동안 Shield-to-POP 트래픽 [은(는) 압축되지 않습니다](https://www.fastly.com/documentation/guides/concepts/compression/#compression-at-the-edge). Edge Side Includes([ESI](https://www.fastly.com/documentation/reference/vcl/statements/esi/)) 지원을 유지하기 위해. 즉, FPC(Full Page Cache) 적중률이 낮으면 동적 페이지에서 훨씬 높은 대역폭을 사용합니다. **FPC 적중률**, 도메인별 **FPC 통계** 및 **CDN 네트워크 세그먼트 대역폭**&#x200B;으로 증상을 확인합니다.

낮은 적중률은 종종 많은 양의 검색 엔진 웹 크롤러에 의해 좌우됩니다([검색 봇 및 웹 크롤러](#search-bots-and-crawlers) 참조). 또 다른 완화 방법은 사용 가능한 경우 [오래된 캐시를 웹 크롤러에게 제공](https://www.fastly.com/documentation/reference/vcl/variables/cache-object/stale-exists/)하는 것입니다. 광범위하고 빈번한 캐시 무효화가 원인인 경우 **태그별 캐시 무효화** 및 **상위 URL별 FPC 수명**&#x200B;을 사용하여 이탈된 태그/URL을 찾으십시오.

## 보트 및 웹 크롤러 검색

웹 크롤러 영향을 측정하려면 **대역폭별 알려진 봇** 및 **알려진 봇 영향 세부 정보**&#x200B;에서 시작하여 가장 활성화된 봇을 확인한 다음 특정 봇을 기준으로 [필터링](https://docs.newrelic.com/docs/query-your-data/explore-query-data/dashboards/filter-new-relic-one-dashboards-facets/#example-use)하여 해당 요청만 검토하십시오.

### 요청이 너무 많음

검색 봇이 너무 많은 요청을 보내는 가장 일반적인 원인은 `<meta name="robots" content="index,follow">`을(를) 전달하는 페이지를 구문 분석하는 동안 발생합니다. 봇은 거의 무한 루프에 있는 위쪽 탐색 및 계층화된 탐색 링크를 따라갈 수 있습니다. 이 문제를 해결하려면 다음 옵션을 고려하십시오.

>[!WARNING]
>
> 웹 크롤러 활동을 억제하기 전에 SEO(검색 엔진 최적화) 전문가에게 문의하십시오. 재교육은 SEO에 부정적인 영향을 미칠 수 있습니다.

- `<a rel="nofollow" href="https://example.com/sales.html">Sales</a>`과(와) 같은 위쪽 탐색 및 계층화된 탐색 링크에 `nofollow`을(를) 추가합니다.
- 페이지 메타 태그를 일반적인 [디자인 구성 설정](https://experienceleague.adobe.com/en/docs/commerce-admin/marketing/seo/seo-overview#configure-robotstxt) 또는 사용자 지정 확장의 페이지 유형별로 `index,nofollow`(으)로 변경합니다. 봇이 항상 인덱싱할 최신 페이지 목록을 갖도록 `sitemap.xml`을(를) 정확하게 유지하십시오.
- `robots.txt`을(를) 업데이트하여 경로에 액세스하면 안 되는 리소스 봇을 차단합니다.
- `crawl-delay` 지시문은 공식 Robots Exclusion Protocol의 일부가 아니지만 Bingbot, Slup, SEMrushBot 및 기타 몇 가지 보트와 같은 일부 봇에 대해 작동합니다. Googlebot은 이 지시문을 무시합니다.
- 비율 제한 규칙을 추가합니다. Fastly 모듈에는 네이티브 [남용 웹 크롤러 보호](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/RATE-LIMITING.md#abusive-crawler-protection)가 있습니다. 보다 세밀하게 조정하기 위해 [사용자 지정 VCL(Varnish Configuration Language) 코드 조각](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/custom-vcl-snippets/fastly-vcl-custom-snippets)은 개별 속도 제한이 있는 사용자 에이전트 정규식에 대해 `429`(요청이 너무 많음) 또는 `405`(메서드가 허용되지 않음)을 반환할 수 있습니다. 웹 크롤러 설명서에서 기본 메서드 및 응답 코드를 확인합니다. Fastly의 [속도 제한 VCL 지침](https://www.fastly.com/documentation/reference/vcl/functions/rate-limiting/ratelimit-check-rate/)을 참조하세요.
- AI와 대형 언어 모델(LLM) 웹 크롤러는 점점 더 커지는 특별한 사례입니다. 항상 자신을 일관되게 식별하지는 않으므로 VCL 사용자 에이전트 규칙이 늦어질 수 있습니다. Adobe의 [고급 보안](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/advanced-security) 추가 기능에는 VCL만으로는 확인할 수 없는 엣지에서 확인된 AI 웹 크롤러 및 페처를 구분할 수 있는 [기본 보트 관리](#advanced-security-native-bot-management-layer-7-ddos--rate-limiting)가 있습니다.

### 원치 않는 웹 크롤러 차단

특정 검색 엔진이 상당한 트래픽을 발생시키고 비즈니스에 중요하지 않은 경우 완전히 차단될 수 있습니다.

- 일부 봇은 구문 분석 규칙을 다시 읽고 업데이트한 후 1~2일 후 `robots.txt` 변경 사항을 따릅니다.
- 웹 크롤러가 `robots.txt`을(를) 무시하는 경우 사용자 지정 VCL 코드 조각([예제](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/how-to/block-malicious-traffic-for-magento-commerce-on-fastly-level#block-traffic-by-user-agent))으로 차단하십시오. 일부 웹 크롤러는 이를 주파수 제어의 선호되거나 유일한 방법으로 명시적으로 기술한다.

## 악의적인 스크립트 및 스크레이퍼

트래픽 인사이트 앱을 사용하여 공격의 일반적인 방향을 식별하고 필요에 따라 포커스 영역별로 필터링합니다. 빨간색 플래그가 지정된 요청이 주로 특정 IP, 서브넷 또는 지리적 위치(**요청별 상위 IP 수**, **IP 서브넷별 통계**, **국가별 통계**)에서 오는 경우 사용자 지정 Fastly VCL로 차단하는 것을 고려하십시오.

모든 클라우드 인프라 프로젝트에는 수행하는 구성에 관계없이 자동 보호의 기준선이 이미 있습니다. 포함된 웹 응용 프로그램 방화벽(WAF)은 SQL 삽입 및 알려진 악의적인 IP 신호(백도어, 공격 도구, CMDEXE, Log4J-JNDI, 트래버스, XSS)를 즉시 차단하며 다른 비악의적인 IP가 50분, 350요청/10분 또는 1,800개 요청/시간을 넘으면 속도를 제한합니다. 이 기준선은 이 앱 테이블의 **WAF 응답별 요청** 및 WAF 신호 열에 표시되는 기준선입니다. 이러한 열이 급증한다고 해서 보호받지 못하는 것은 아닙니다.

- 자격 증명 스터핑, 계정 탈취, 가짜 계정 만들기, 카드 테스트, 콘텐츠 스크래핑 및 인벤토리/장바구니 사재기를 확인하십시오. 이러한 보트 기반 남용 패턴은 **보트 활동 및 요청 분석** 탭에 표시됩니다. 로그인, 계정, 체크아웃 또는 카탈로그 끝점에 도달하는 대량의 낮은 다양성 트래픽은 **요청별 상위 IP 수** 및 **알려진 봇 영향 세부 정보**&#x200B;에서 찾을 시그니처입니다.
- [Google reCAPTCHA](https://experienceleague.adobe.com/en/docs/commerce-admin/systems/security/captcha/security-google-recaptcha)을(를) 사용하여 봇 공격으로부터 체크아웃 및 체크아웃 API 끝점을 보호합니다.
- Fastly 모듈의 기본 속도 제한 [경로 보호](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/RATE-LIMITING.md#path-protection)를 사용하십시오.
- 쉼표로 구분된 `Sigsci_Tags` 필드에서 [다음 세대 WAF 신호](https://www.fastly.com/documentation/guides/next-gen-waf/signals/using-system-signals/)를 확인하고 관련 신호 일치를 타깃팅된 차단 규칙에 결합하십시오. 의심스러운 요청의 값은 `BOT-ANALYSIS,DATACENTER,SIGSCI-IP,SITE-FLAGGED-IP,SUSPECTED-BAD-BOT`과(와) 비슷합니다. WAF은 자동으로 차단을 시작하기 전에 임계값까지 `SITE-FLAGGED-IP`을(를) 사용하는 IP에 레이블을 지정합니다. **WAF 공격 및 예외 항목 신호**, **WAF 봇 신호** 및 **WAF 응답별 요청** 위젯과 IP, 서브넷 및 국가 테이블의 WAF 열에 표시됩니다.
- 일반적인 접근 방식은 [Fastly 수준에서 Adobe Commerce에 대한 악성 트래픽 차단](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/how-to/block-malicious-traffic-for-magento-commerce-on-fastly-level)에 대한 Adobe의 문서를 참조하십시오.
- 지속적인 보트 캠페인, 많은 IP/API에 퍼진 공격 또는 L7 분산 서비스 거부(DDoS)와 같이 수동 차단이 실행 가능하지 않은 복잡한 시나리오의 경우 먼저 Adobe의 [고급 보안](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/advanced-security) 추가 기능을 고려하십시오([기본 보트 관리](#advanced-security-native-bot-management-layer-7-ddos--rate-limiting) 참조). 상점을 제공하는 동일한 Fastly 에지에서 실행됩니다. 범위를 벗어난 기능이 필요한 경우 [Datadome](https://docs.datadome.co/docs/module-fastly) 또는 [HUMAN Bot Defender](https://www.fastly.com/documentation/guides/integrations/non-fastly-services/human-bot-defender/)&#x200B;(이전 PerimeterX)과 같이 기본 Fastly 통합을 사용하는 서드파티 관리 봇 완화 서비스를 사용하는 것이 좋습니다. 이러한 모든 옵션은 추가 비용을 추가합니다.

## 고급 보안: 기본 보트 관리, L7 DDoS 및 속도 제한

이전 섹션에서는 트래픽 인사이트 앱의 데이터 및 수동 Fastly VCL을 사용하여 수행할 수 있는 작업에 대해 설명합니다. 지속 또는 진화하는 보트 캠페인, 레이어 7(애플리케이션 레이어) DDoS 또는 남용이 여러 IP 및 API 엔드포인트에서 얇게 확산되는 경우와 같이 충분하지 않은 시나리오의 경우 Adobe에서 [고급 보안](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/advanced-security)을 제공합니다.

Advanced Security는 이미 상점을 제공하고 있는 동일한 Fastly 플랫폼에서 에지 보트 관리(AI 웹 크롤러 및 페처 탐지 포함), Layer 7 DDoS 보호 및 고급 속도 제한을 추가하는 [!DNL Adobe Commerce on Cloud Infrastructure]용 유료 추가 기능입니다. 전체 기능, 현재 제한 사항 및 요청 방법은 [고급 보안](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/advanced-security)을 참조하세요.

구매하고 활성화하면 트래픽 인사이트 앱을 사용하여 고급 보안이 작동하는지 확인합니다. 해당 결정은 **WAF 공격 및 예외 항목 신호**, **WAF 봇 신호** 및 **WAF 응답별 요청** 뒤에 있는 동일한 `Sigsci_Tags` 및 `Agent_response` 필드를 통해 보고됩니다. 활성화 전/후의 해당 위젯을 비교하여 이 현재 트래픽에 적극적으로 작용하고 있는지 확인합니다.
