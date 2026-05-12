---
title: Adobe Commerce 고급 보안
description: 고급 보안이 클라우드 인프라의 Adobe Commerce에 보트 관리, 고급 속도 제한 및 L7 DDoS 보호를 추가하는 방법에 대해 알아봅니다.
feature: Cloud, Configuration, Security
source-git-commit: 8a7c1c297092fdf2b75d22ce99c360c85eac0495
workflow-type: tm+mt
source-wordcount: '1986'
ht-degree: 0%

---

# [!DNL Adobe Commerce Advanced Security]

[!DNL Adobe Commerce Advanced Security]은(는) [!DNL Adobe Commerce on Cloud Infrastructure]과(와) 함께 온라인 스토어를 빠르고, 사용 가능하며, 안전하게 유지하는 제품입니다. 이를 통해 최대 트래픽 이벤트 및 자동화된 공격 동안 매출을 보호하고 다운타임을 줄이고 고객의 신뢰를 유지하는 데 도움이 될 수 있습니다.

[!DNL Adobe Commerce on Cloud Infrastructure]에 기본 제공 [레이어 3 및 4 DDoS 보호](./fastly.md#ddos-protection) 및 [웹 응용 프로그램 방화벽(WAF)](./fastly-waf-service.md)이(가) 포함되어 있습니다. [공유 책임 모델](https://experienceleague.adobe.com/ko/docs/commerce-operations/security-and-compliance/shared-responsibility)에서 L7 DDoS 탐지, 보트 보호 및 사전 IP 차단은 판매자 책임이며, [!DNL Adobe Commerce Advanced Security]은(는) 이를 해결하기 위해 설계되었습니다.

[!DNL Advanced Security]은(는) 네트워크 에지에서 규모, 성능 및 보안을 결합하는 통합 에지 플랫폼의 일부로 보트 관리, 고급 속도 제한 및 레이어 7 DDoS 보호를 제공하는 Fastly 기반 에지 보안 기능을 통해 상점 보호를 확장합니다.

>[!NOTE]
>
>[!DNL Advanced Security]은(는) [!DNL Adobe Commerce on Cloud Infrastructure]&#x200B;(PaaS) 프로젝트에만 사용할 수 있습니다.

## 핵심 기능

[!DNL Adobe Commerce Advanced Security]에는 다음과 같은 추가 보호가 포함되어 있습니다.

- **[보트 관리](https://docs.fastly.com/products/bot-management)**—웹 응용 프로그램에서 원치 않는 보트 활동을 식별하고 완화합니다. 봇 관리 서비스는 합법적인 봇(검색 엔진 웹 크롤러, 소셜 미디어 봇)과 악의적인 봇을 구별하여, 네트워크 에지에서 트래픽을 차단, 허용, 챌린지 또는 속도 제한 등의 옵션을 통해 실시간 분류를 제공합니다.

- **[DDoS 보호](https://docs.fastly.com/products/fastly-ddos-protection)**—모든 [!DNL Adobe Commerce on Cloud Infrastructure] 프로젝트에 포함된 기존 레이어 3 및 4 보호 이상의 레이어 7(응용 프로그램 레이어) DDoS 보호를 제공합니다. DDoS Protection 서비스는 대규모 볼륨 공격을 흡수하고 DDoS(Distributed Denial-of-Service) 이벤트 동안 지속적인 애플리케이션 가용성을 보장하여 트래픽 최대 기간 동안 매출을 보호합니다.

- **[고급 속도 제한](https://www.fastly.com/documentation/guides/next-gen-waf/rules/working-with-advanced-rate-limiting-rules/)**—특정 URL, API 끝점 및 응용 프로그램 리소스를 남용으로부터 보호하는 구성 가능한 속도 제한 규칙을 제공합니다. Advanced Rate Limiting 서비스는 Fastly CDN 모듈을 통해 사용할 수 있는 [기본 속도 제한](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/RATE-LIMITING.md)을 넘어 특정 트래픽 패턴 및 공격 벡터를 타겟팅하므로 인프라 긴장과 클라우드 비용을 줄입니다.

>[!NOTE]
>
>현재 [!DNL Advanced Security] 구성에 지원 티켓을 제출해야 합니다. 관리 UI를 통한 셀프서비스 구성은 향후 릴리스에 포함될 예정입니다. 자세한 내용은 [요청 [!DNL Advanced Security]](#request-advanced-security)을 참조하세요.

## 위협 범위

[!DNL Advanced Security]은(는) 자동화된 응용 프로그램 계층 위협으로부터 상점 전면을 보호합니다.

![Adobe Commerce 보안 스택에서 고급 보안 위치 지정](../../assets/advanced-security.svg)

### 봇 주도 남용

- **자격 증명 스터핑**—데이터 침해에서 도난된 자격 증명을 사용하여 자동으로 로그인을 시도합니다.
- **계정 인계**—고객 계정에 대한 무단 액세스를 시도하는 봇입니다.
- **계정 만들기 남용**—사기 또는 남용 목적으로 가짜 계정을 자동으로 만듭니다.
- **카드 테스트**—도난 당한 신용 카드 번호를 결제 프로세서에 대해 테스트하는 봇입니다.
- **컨텐츠 스크래핑** - 스토어에서 제품 데이터, 가격 또는 컨텐츠의 자동 추출.
- **재고 보관** - 적법한 구매를 방지하기 위해 제품을 장바구니에 담는 봇입니다.

### AI 보트 관리

- **AI 웹 크롤러 감지** - 동의 없이 대용량 언어 모델을 교육하기 위해 콘텐츠를 스크랩하는 AI 웹 크롤러를 식별하고 관리합니다.
- **AI 페처 컨트롤** - 실시간 AI 검색 결과에 사용되는 AI 페처를 제어합니다.
- **구성 가능한 AI 보트 정책** - 정책 적용을 위해 구성 가능한 신호 유형을 사용하여 확인된 AI 보트와 의심되는 AI 보트를 구별합니다.

### 애플리케이션 계층 공격

- **레이어 7 DDoS 공격** - 기본 제공 레이어 3 및 4 보호를 무시하는 응용 프로그램 레이어를 대상으로 하는 분산 공격입니다. [!DNL Advanced Security]은(는) 원본 서버에 도달하기 전에 가장자리에서 이러한 볼륨 공격을 흡수합니다.
- **URL 및 API 남용** - 다수의 IP 주소에 퍼진 특정 URL 또는 API 끝점을 타깃팅하는 공격으로서, 개별 IP 차단이 효과적이지 않습니다.
- **캐시 무효화 공격** - CDN 캐싱을 무시하고 원본 서버를 압도하도록 설계된 조작된 쿼리 매개 변수를 사용한 요청입니다.

### 추가 기능

- **동적 문제** - 의심스러운 트래픽에 최적의 문제를 자동으로 할당합니다. PAT(Private Access Tokens)를 활용하여 사용자 경험에 영향을 주지 않고 요청의 일부를 원활하게 확인할 수 있습니다.
- **기만 기술**—공격자에게 거짓 정보를 반환하여 공격자의 공격을 완화하고 대규모로 운영하는 능력을 방해하여 계정 탈취 시도를 해결합니다.

## 올바른 보호 선택

다음 지침을 사용하여 [!DNL Advanced Security]이(가) 상점 보호 요구 사항에 적합한 솔루션인지 여부 또는 기존 보호 또는 대체 솔루션이 더 적합한지 여부를 판단하십시오.

### [!DNL Advanced Security] 사용 시기

다음 시나리오는 [!DNL Advanced Security]&#x200B;(으)로 가장 잘 해결됩니다.

| 시나리오 | [!DNL Advanced Security]의 도움말 |
|---|---|
| 사이트에서 자격 증명 스터핑, 콘텐츠 스크래핑 또는 인벤토리 저장과 같은 봇 기반 공격이 발생합니다 | 봇 관리는 애플리케이션에 도달하기 전에 엣지에서 자동화된 위협을 식별하고 완화합니다 |
| L3 및 L4 범위 이상의 L7 DDoS 보호 기능 필요 | DDoS 보호는 네트워크 수준 보호를 사용하지 않는 애플리케이션 계층 공격을 흡수합니다. |
| 특정 URL 또는 API 끝점은 IP로 차단할 수 없는 대량 분산 트래픽에 의해 타깃팅됩니다 | 고급 속도 제한은 특정 끝점 및 트래픽 패턴에 대한 세분화된 제어를 제공합니다 |
| 상점 첫 화면 콘텐츠에 액세스하는 AI 웹 크롤러 및 페처를 관리하려는 경우 | 보트 관리에는 구성 가능한 AI 보트 탐지 및 적용 정책이 포함되어 있습니다 |
| 기존 Fastly CDN과 통합된 Adobe 지원 에지 보안 솔루션이 필요합니다 | [!DNL Advanced Security]은(는) 이미 상점을 제공하는 동일한 Fastly 에지 플랫폼에서 실행됩니다. |

### 기존 보호 사용 시기

다음 시나리오는 기존 보호를 사용하여 가장 잘 해결됩니다.

| 시나리오 | 권장 접근 방식 |
|---|---|
| 단일 IP 또는 식별 가능한 작은 IP 세트가 요청으로 사이트를 범람하고 있습니다 | Commerce 관리 또는 Fastly API를 사용하여 IP를 차단합니다. 기본 제공 [Layer 3/4 DDoS 보호](./fastly.md#ddos-protection) 및 기존 [IP 차단 목록에 추가하다](./fastly-vcl-blocking.md) VCL 코드 조각을 사용하십시오. |
| SQL 삽입, XSS(크로스 사이트 스크립팅) 또는 기타 OWASP Top Ten 위협을 차단해야 합니다 | 포함된 [WAF 서비스](./fastly-waf-service.md)는 이러한 위협을 자동으로 차단합니다. |
| 기본 VCL 차단 규칙으로 DDoS 공격 패턴을 제어할 수 있습니다 | Adobe Commerce에서 이미 사용 가능한 기존 [사용자 지정 VCL 코드 조각](./fastly-vcl-custom-snippets.md)을 사용하십시오. |

### 대체 보호를 사용해야 하는 경우

다음 시나리오는 [!DNL Advanced Security]을(를) 보완할 수 있는 대체 보호를 사용하여 가장 적절하게 처리됩니다.

| 시나리오 | 권장 접근 방식 |
|---|---|
| 거래 수준 사기 점수 책정 또는 결제 사기 방지 기능이 필요합니다. | 전용 사기 방지 플랫폼을 사용합니다. [!DNL Advanced Security]은(는) 에지 네트워크 수준에서 보호되며 개별 결제 거래를 평가하지 않습니다. |
| ID 및 액세스 관리(IAM) 필요 | 전용 IAM 솔루션 구현 사용자 인증 및 세션 관리는 고객의 책임으로 유지됩니다. |
| 정적 또는 동적 애플리케이션 보안 테스트(SAST/DAST)가 필요합니다. | 전용 애플리케이션 보안 테스트 도구를 사용합니다. 코드 수준 취약성 검사가 제공되지 않았습니다. |
| 속도 제한을 넘어서는 포괄적인 API 보안이 필요합니다(예: 스키마 유효성 검사 또는 API 게이트웨이 기능) | 전용 API 보안 플랫폼을 고려해 보십시오. |
| PCI 스캔 또는 SOC 보고와 같은 규정 준수 툴이 필요합니다. | 전용 규정 준수 관리 도구를 사용합니다. |

>[!TIP]
>
>현재 서드파티 보트 보호 공급자를 사용하는 경우 [!DNL Advanced Security]에 통합하면 운영 복잡성이 줄어들고 공급자 간에 일관되지 않은 보안 적용 범위가 제거될 수 있습니다. 프로젝트에 대한 [!DNL Advanced Security]을(를) 평가하려면 Adobe 계정 팀에 문의하세요.

## 보안 스택 포지셔닝

[!DNL Advanced Security]은(는) 에지 기반 보호의 추가 계층으로 광범위한 Adobe Commerce 보안 아키텍처에 적합합니다. 이 기능은 [!DNL Adobe Commerce on Cloud Infrastructure]에 이미 포함된 WAF 및 Layer 3/4 DDoS 보호와 함께 작동하며, 이를 대체하지 않습니다. 다음 섹션에서는 기존 보호 기능 및 고객에게 남아 있는 책임과 어떻게 관련되어 있는지 설명합니다.

### 포함된 보호 기능

[!DNL Adobe Commerce on Cloud Infrastructure]에는 다음 보안 기능이 포함되어 있습니다.

- **[웹 응용 프로그램 방화벽(WAF)](./fastly-waf-service.md)**—SQL 삽입, XSS(교차 사이트 스크립팅) 및 기타 OWASP(Open Web Application Security Project)에 대한 관리되는 보호 상위 10가지 위협입니다. 프로덕션 환경에서만 사용할 수 있습니다.
- **[레이어 3 및 4 DDoS 보호](./fastly.md#ddos-protection)**—SYN 플러시, UDP 플러시, ICMP 기반 공격 및 TCP 수준 공격과 같은 네트워크 레이어 공격에 대한 보호 기능이 내장되어 있습니다. Fastly CDN을 사용하여 자동으로 활성화됩니다.
- **[SSL/TLS 인증서](./fastly-configuration.md#provision-ssltls-certificates)**—보안 HTTPS 트래픽에 대한 도메인 유효성 검사 암호화 인증서.
- **[원본 차단](./fastly.md#origin-cloaking)**—Fastly를 통한 모든 트래픽 경로를 보장하여 원본 서버에 대한 직접 액세스를 차단합니다.
- **[VCL 기반 보안 조각](./fastly-vcl-custom-snippets.md)**—IP 차단, 허용 목록에 추가 및 요청 필터링에 대한 사용자 지정 VCL(Varnish Configuration Language) 규칙입니다.

### [!DNL Advanced Security]

[!DNL Advanced Security]은(는) [!DNL Adobe Commerce on Cloud Infrastructure]에 포함된 기본 제공 보호 이상의 향상된 보호를 제공하지만 추가 비용으로:

- **보트 관리**—AI 보트 관리를 통한 Edge 기반 보트 탐지 및 완화.
- **레이어 7 DDoS 보호**—응용 프로그램 레이어 DDoS 흡수 및 방어.
- **고급 속도 제한** - URL 및 API 끝점에 대한 세분화된 속도 제어.
- **동적 문제 및 기만적 기술**—자동화된 문제 할당 및 계정 인수 완화.

### 고객 책임

- **사기 방지** - 거래 수준 사기 점수 및 결제 사기 감지.
- **ID 및 액세스 관리**—고객 인증, 권한 부여 및 세션 관리.
- **응용 프로그램 보안 테스트**—SAST/DAST 및 취약성 검사.
- **사용자 지정 보안 구성**—VCL 기반 규칙, IP 허용 목록 및 차단 목록.
- **규정 준수 도구**—PCI 스캔, SOC 규정 준수 보고 및 규정 감사 도구.
- **응용 프로그램 수준 강화**—토큰 기반 API 인증, 쿼리 매개 변수 정규화 및 캐싱 전략 디자인입니다.

Adobe 및 고객 보안 책임에 대한 전체 개요는 [공유 책임 모델](https://experienceleague.adobe.com/ko/docs/commerce-operations/security-and-compliance/shared-responsibility)을 참조하십시오.

## 일반적인 공격 패턴 및 보호

다음 표는 일반적인 공격 패턴을 Adobe Commerce 보안 스택 내의 적절한 보호 계층에 매핑합니다.

| 공격 패턴 | 유형 | 보호 |
|---|---|---|
| 단일 IP 또는 다수의 요청을 전송하는 식별 가능한 IP 집합 | DDoS + 보트 | Commerce 관리 또는 Fastly API를 사용하여 IP를 차단합니다. 내장된 L3/4 DDoS 보호 기능은 네트워크 에지에서 이 트래픽을 필터링합니다. |
| 특정 URL 또는 API에 대한 공격이 다수의 IP에 퍼짐 | DDoS + 보트 | **[!DNL Advanced Security]**: 고급 속도 제한은 URL당 요청 볼륨을 제한합니다. 보트 관리는 배포된 보트 트래픽을 식별하고 차단합니다. |
| 적절한 인증 없이 REST API 엔드포인트에 대한 자동화된 공격 | 보트 + DDoS | API 종단점이 토큰 기반 인증을 사용하는지 확인하십시오. 토큰이 손상된 경우 자격 증명을 회전합니다. **[!DNL Advanced Security]**: 고급 속도 제한은 노출된 끝점을 보호할 수 있습니다. |
| 조작된 쿼리 매개변수를 사용하는 캐시 무효화 공격 | 보트 + DDoS | 캐시 키에서 필수적이지 않은 쿼리 매개 변수를 제외합니다. 응용 프로그램 수준에서 쿼리 매개 변수를 정규화하고 제한합니다. **[!DNL Advanced Security]**: 보트 관리가 자동 캐시 무효화 트래픽을 감지하고 차단합니다. |
| SQL 삽입 또는 XSS(크로스 사이트 스크립팅) 시도 | WAF | 포함된 [WAF 서비스](./fastly-waf-service.md)는 관리되는 보안 규칙을 사용하여 이러한 위협을 자동으로 차단합니다. |

### WAF 차단 동작

다음 WAF 동작은 [!DNL Advanced Security]의 사용 여부와 관계없이 모든 [!DNL Adobe Commerce on Cloud Infrastructure] 프로젝트에 적용됩니다. 포함된 WAF 서비스는 일반적인 공격 신호에 대해 다음 차단 동작을 사용합니다.

- 일치하는 단일 요청의 경우에도 **SQL 삽입** 요청이 즉시 차단됩니다.
- 알려진 악성 IP의 위협 신호(백도어, 공격 도구, CMDEXE, Log4J JNDI, Traversal, XSS)로 식별된 요청은 즉시 차단됩니다.
- 위의 위협 신호를 표시하는 비악성 IP의 요청은 다음 임계값을 초과할 때 차단됩니다.

| 간격 | 임계값 | 빈도 확인 |
|---|---|---|
| 1분 | 50개 요청 | 20초마다 |
| 10분 | 350개 요청 | 3분마다 |
| 1시간 | 1,800개 요청 | 20분마다 |

## [!DNL Advanced Security] 요청

[!DNL Advanced Security]을(를) 요청하려면

>[!NOTE]
>
>[!DNL Advanced Security]은(는) 추가 비용으로 사용할 수 있으며 활성 [!DNL Adobe Commerce on Cloud Infrastructure]&#x200B;(PaaS) 구독이 필요합니다.

1. 프로젝트의 [!DNL Advanced Security]에 대해 논의하려면 Adobe 계정 팀이나 Adobe 영업 담당자에게 문의하십시오.

1. [!DNL Advanced Security]을(를) 구매한 후 [!DNL Advanced Security] 활성화를 요청하는 [Adobe Commerce 지원 티켓을 제출](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html?lang=ko#submit-ticket)합니다. [!DNL Adobe Commerce on Cloud Infrastructure] 프로젝트 ID와 지원이 필요한 환경(예: 프로덕션 및 스테이징)을 포함하십시오.

1. Adobe은 Fastly 서비스에서 [!DNL Advanced Security]을(를) 활성화하고 초기 보호 정책을 구성합니다. 활성화는 일반적으로 티켓 제출 후 영업일 기준으로 몇 일 이내에 완료됩니다.

1. [!DNL Advanced Security]이(가) 활성 상태임을 확인하고 환경에 대해 활성화된 보호 기능에 대한 세부 정보를 받게 됩니다.

>[!NOTE]
>
>[!DNL Advanced Security]에 대한 구성을 변경하려면 현재 [지원 티켓을 제출](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html?lang=ko#submit-ticket)해야 합니다. 관리 UI를 통한 셀프서비스 구성은 향후 릴리스에 포함될 예정입니다.

## 제한 사항

[!DNL Advanced Security]은(는) Edge-Layer Storefront 보호를 제공합니다. 다음 기능은 사용할 수 없으며 보완 솔루션으로 가장 잘 해결됩니다.

- **거래 수준 사기 점수**—[!DNL Advanced Security]는 사기 위험에 대한 개별 결제 거래를 평가하지 않습니다. 거래 수준 채점을 위해 전용 사기 방지 플랫폼을 사용합니다.
- **IAM(Identity and Access Management)**—[!DNL Advanced Security]은(는) 사용자 인증, 권한 부여 또는 세션 관리를 관리하지 않습니다. 이는 고객의 책임입니다.
- **SAST/DAST(정적 및 동적 응용 프로그램 보안 테스트)**—[!DNL Advanced Security]에는 코드 수준 취약성 검사 또는 침투 테스트가 포함되어 있지 않습니다.
- **API 보안** - 고급 속도 제한은 API 끝점을 남용으로부터 보호할 수 있지만 스키마 유효성 검사 및 API 게이트웨이 관리와 같은 포괄적인 API 보안 기능은 제공되지 않습니다.
- **전체 사기 방지**—[!DNL Advanced Security]는 Edge-Layer 상점 보호에 중점을 두며 완전한 사기 행위 관리 플랫폼이 아닙니다.
- **규정 준수 도구**—[!DNL Advanced Security]는 PCI 스캔, SOC 규정 준수 보고 또는 규정 감사 기능을 제공하지 않습니다.
