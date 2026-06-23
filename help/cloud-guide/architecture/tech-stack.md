---
title: 기술 스택
description: 클라우드 인프라에서 Commerce을 구성하는 기술 스택을 참조하십시오.
feature: Cloud, Iaas, Paas
exl-id: 3fac1ab7-6440-4bf9-8169-9fadf51d70dd
TQID: https://experienceleague.adobe.com/2-uZdx1Oi-3LQUK-L7rC4kZWYcYobEhnVcUDNwOHpFs
product_v2:
  - id: eadea719-cf89-469b-a6fd-a236a7138047
feature_v2:
  - id: b5f00040-57a0-4a6d-a39e-383b1936c2c9
  - id: ba9e5be9-7de1-4f71-a5d2-baead0e425ee
  - id: c1256247-af4b-46d8-9dca-0c654ecfa157
  - id: d1e21356-0064-4f48-9089-16e3f0dbd2a6
  - id: e8818fe6-9c8b-4bc0-9ef8-377a10b7bc75
subfeature_v2:
  - id: df5e974b-6742-4873-a687-a6bedaafdaa2
  - id: f2261633-201d-46c5-8a66-999e70527a83
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
topic_v2:
  - id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
source-git-commit: d863fc70609dcc66d21eb95e709db80e29114714
workflow-type: tm+mt
source-wordcount: 405
ht-degree: 0%

---

# 기술 스택

아래와 같이 클라우드 인프라의 Adobe Commerce을 5개의 기능 계층으로 생각해 보십시오.

![클라우드 스택](../../assets/CloudStack.png)

1. [**클라우드 인프라**](pro-architecture.md): Adobe Commerce on cloud infrastructure Pro 프로젝트를 위해 Amazon Web Services(AWS) 또는 Microsoft Azure을 IaaS(Infrastructure as a Service) 기반으로 선택합니다.

   Adobe은 정기적으로 가상 컴퓨팅 리소스(vCPU) 사용을 분석하고 리소스를 자동으로 할당하여 장기 사용을 최적화하고 최대 연간 vCPU 일 허용량을 초과할 위험을 완화합니다. 특정 기간 동안 사이트 트래픽이 증가할 것으로 예상되면 [임시 업사이징을 요청](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/how-to-request-temporary-magento-upsize.html?lang=ko)할 지원 티켓을 계속 열어야 합니다.

1. [**Platform as a Service**](cloud-architecture.md): 클라우드 인프라 프로젝트의 각 Adobe Commerce은 서비스 개발, 테스트 및 통합을 위한 PaaS(Platform as a Service) 통합 환경을 제공합니다.
1. [**Adobe Commerce**](../project/overview.md): 클라우드 인프라의 Adobe Commerce은 PHP, MySQL(MariaDB), Redis, 메시지 큐 서비스([!DNL RabbitMQ] 또는 [!DNL ActiveMQ]) 및 지원되는 검색 엔진 기술을 포함하는 사전 제공된 인프라를 제공합니다.
1. [**성능 도구**](../monitor/new-relic-service.md): New Relic 성능 도구를 사용하면 Adobe Commerce에서 클라우드 인프라 프로젝트를 통해 데이터를 수집, 분석 및 표시하여 응용 프로그램과 인프라를 디버그, 모니터링 및 관리할 수 있습니다.
1. [**CDN(콘텐츠 전송 네트워크), 웹 응용 프로그램 방화벽([!DNL WAF]) 및 이미지 최적화(IO)**](../cdn/fastly.md):

   * [Fastly CDN](../cdn/fastly.md#ddos-protection) - [!DNL Ping of Death], [!DNL Smurf] 공격 및 기타 ICMP(Internet Control Message Protocol) 기반 플러드 공격과 같은 DDoS(Distributed Denial of Service) 공격으로부터 안전하게 보호하는 CDN 서비스를 제공합니다.
   * [웹 응용 프로그램 방화벽(WAF)](../cdn/fastly-waf-service.md)—WAF 서비스는 프로덕션 환경의 Adobe Commerce 상점 및 WAF 정책에 대한 PCI 규정을 준수하여 주입 공격, 악의적인 입력, 교차 사이트 스크립팅, 데이터 내보내기, HTTP 프로토콜 위반 및 기타 [[!DNL OWASP] 10대 보안 위협](https://owasp.org/www-project-top-ten/)으로부터 Adobe Commerce 웹 응용 프로그램을 보호합니다.
   * [이미지 최적화(IO)](../cdn/fastly-image-optimization.md) - 실시간 이미지 조작 및 최적화를 제공하여 이미지 전달 속도를 높이고 응답형 웹 애플리케이션의 이미지 소스 세트 유지 관리를 간소화합니다. 신속한 IO는 이미지 처리 및 크기 조정 부하를 분산시켜 서버가 주문 및 전환을 효율적으로 처리할 수 있도록 합니다.

모노리식 응용 프로그램은 리소스 집약적이고 확장과 신속한 서비스가 어렵습니다. Commerce 고객은 클라우드 인프라를 통해 풍부하고 지능적이며 성능이 뛰어난 SaaS 기반 마이크로서비스에 매우 쉽게 액세스할 수 있습니다. [지원되는 소프트웨어 및 서비스](cloud-architecture.md#supported-software-and-services)를 참조하십시오.

[Commerce 시작 안내서](../../get-started/overview.md)를 사용하여 새 클라우드 프로그램을 설정하고 클라우드 기반 환경에서 [!DNL Commerce] 응용 프로그램을 관리할 수 있습니다.

