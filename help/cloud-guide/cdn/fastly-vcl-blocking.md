---
title: 차단 요청에 대한 사용자 지정 VCL
description: 사용자 지정 VCL 코드 조각과 함께 Edge ACL(액세스 제어 목록)을 사용하여 IP 주소별 수신 요청을 차단합니다.
feature: Cloud, Configuration, Security
exl-id: eb21c166-21ae-4404-85d9-c3a26137f82c
source-git-commit: d08ef7d46e3b94ae54ee99aa63de1b267f4e94a0
workflow-type: tm+mt
source-wordcount: '996'
ht-degree: 0%

---

# 차단 요청에 대한 사용자 지정 VCL

Magento 2용 Fastly CDN 모듈을 사용하여 차단하려는 IP 주소 목록과 함께 Edge ACL을 만들 수 있습니다. 그런 다음 VCL 코드 조각과 함께 해당 목록을 사용하여 들어오는 요청을 차단할 수 있습니다. 이 코드는 수신 요청의 IP 주소를 확인합니다. ACL 목록에 포함된 IP 주소와 일치하는 경우 Fastly는 요청이 사이트에 액세스하지 못하도록 차단하고 `403 Forbidden error`을(를) 반환합니다. 다른 모든 클라이언트 IP 주소는 액세스가 허용됩니다.

**필수 구성 요소:**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- 차단할 클라이언트 IP 주소 목록

## 클라이언트 IP 주소 차단을 위한 Edge ACL 만들기

Edge ACL을 만들어 차단할 IP 주소 목록을 정의합니다. ACL을 만든 후에는 사용자 지정 VCL 코드 조각에서 사용하여 스테이징 또는 프로덕션 사이트에 대한 액세스를 관리할 수 있습니다.

두 환경에서 동일한 이름의 Edge ACL을 생성하여 스테이징 및 프로덕션 사이트에 대한 액세스를 관리합니다. VCL 코드 조각 코드는 두 환경에 모두 적용됩니다.

1. 관리자에 로그인합니다.
1. **스토어** > 설정 > **구성** > **고급** > **시스템** > **전체 페이지 캐시** > **빠른 구성**(으)로 이동합니다.
1. **Edge ACL** 섹션을 확장합니다.
1. 목록을 만들려면 **ACL 추가**&#x200B;를 클릭하십시오. 이 예제에서는 목록을 &quot;차단 목록에 추가하다&quot;로 지정합니다.
1. 목록에 IP 주소 값을 입력합니다. 이 목록에 추가된 모든 클라이언트 IP 주소가 차단되어 사이트에 액세스할 수 없습니다.
1. 필요한 경우 **무효화** 확인란을 선택하십시오.

VCL 코드 조각에서 이름별 Edge ACL을 참조합니다.

## 사용자 정의 차단 목록 VCL 생성

>[!NOTE]
>
>이 예는 고급 사용자가 VCL 코드 조각을 만들어 Fastly 서비스에 업로드할 사용자 지정 차단 규칙을 구성하는 방법을 보여줍니다. Magento 2 모듈용 Fastly CDN에서 사용할 수 있는 [차단](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BLOCKING.md) 기능을 사용하여 Adobe Commerce 관리자의 국가 기반 차단 목록에 추가하다 또는 허용 목록에 추가하다를 구성할 수 있습니다.

Edge ACL을 정의한 후 이 ACL을 사용하여 ACL에 지정된 IP 주소에 대한 액세스를 차단하는 VCL 코드 조각을 만들 수 있습니다. 스테이징 및 프로덕션 환경 모두에서 동일한 VCL 코드 조각을 사용할 수 있지만 각 환경에 별도로 코드 조각을 업로드해야 합니다.

차단 목록에 추가하다 다음 사용자 지정 VCL 코드 조각 코드(JSON 형식)는 ACL에 있는 주소와 일치하는 클라이언트 IP 주소를 사용하여 들어오는 요청을 차단하는 논리를 보여 줍니다.

```json
{
  "name": "blocklist",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( client.ip ~ blocklist) { error 403 \"Forbidden\"; }"
}
```

이 예제를 기반으로 코드 조각을 만들기 전에 값을 검토하여 변경해야 하는지 여부를 결정합니다.

- `name`: VCL 코드 조각의 이름입니다. 이 예제에서는 `blocklist` 이름을 사용했습니다.

- `priority`: VCL 코드 조각이 실행되는 시기를 결정합니다. 관리자 요청이 허용된 IP 주소에서 오는지 여부를 즉시 실행하고 확인하는 우선 순위는 `5`입니다. 이 코드 조각은 기본 Magento VCL 코드 조각(`magentomodule_*`)에 우선 순위 50이 할당되기 전에 실행됩니다. 코드 조각을 실행할 시기에 따라 각 사용자 지정 코드 조각의 우선 순위를 50보다 높거나 낮게 설정합니다. 우선 순위가 낮은 번호가 있는 코드 조각이 먼저 실행됩니다.

- `type`: 생성된 VCL 코드에서 코드 조각의 위치를 결정하는 VCL 코드 조각 유형을 지정합니다. 이 예제에서는 `recv`을(를) 사용합니다. 이 은(는) `vcl_recv` 서브루틴에 보일러판 VCL 아래와 모든 개체 위에 VCL 코드를 삽입합니다. 코드 조각 형식 목록은 [Fastly VCL 코드 조각 참조](https://docs.fastly.com/api/config#api-section-snippet)를 참조하십시오.

- `content`: 실행할 VCL 코드 조각으로, 클라이언트 IP 주소를 확인합니다. IP가 Edge ACL에 있으면 전체 웹 사이트에 대해 `403 Forbidden` 오류로 인해 액세스가 차단됩니다. 다른 모든 클라이언트 IP 주소는 액세스가 허용됩니다.

환경에 대한 코드를 검토하고 업데이트한 후 다음 방법 중 하나를 사용하여 사용자 지정 VCL 코드 조각을 Fastly 서비스 구성에 추가합니다.

- [관리자로부터 사용자 지정 VCL 코드 조각을 추가](#add-the-custom-vcl-snippet)합니다. 관리자에 액세스할 수 있는 경우 이 방법이 권장됩니다. ([Fastly 버전 1.2.58](fastly-configuration.md#upgrade-fastly-module) 이상이 필요합니다.)

- JSON 코드 예제를 파일(예: `blocklist.json`)에 저장하고 [Fastly API를 사용하여 업로드](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api)합니다. 관리자에 액세스할 수 없는 경우 이 메서드를 사용합니다.

## 사용자 지정 VCL 코드 조각 추가

{{admin-login-step}}

1. **스토어** > 설정 > **구성** > **고급** > **시스템**&#x200B;을 클릭합니다.

1. **전체 페이지 캐시** > **빠른 구성** > **사용자 지정 VCL 조각**&#x200B;을 확장합니다.

1. **사용자 지정 코드 조각 만들기**&#x200B;를 클릭합니다.

1. VCL 코드 조각 값을 추가합니다.

   - **이름** — `blocklist`

   - **유형** — `recv`

   - **우선 순위** — `5`

   - **VCL** 코드 조각 콘텐츠 추가:

     ```conf
     if ( client.ip ~ blocklist) { error 403 "Forbidden"; }
     ```

1. **만들기**&#x200B;를 클릭하여 이름 패턴이 `type_priority_name.vcl`인 VCL 코드 조각 파일을 생성합니다(예: `recv_5_blocklist.vcl`).

1. 페이지가 다시 로드되면 **Fastly 구성** 섹션에서 *Fastly에 VCL 업로드*&#x200B;를 클릭하여 Fastly 서비스 구성에 파일을 추가하십시오.

1. 업로드 후 페이지 상단의 알림에 따라 캐시를 새로 고칩니다.

업로드 프로세스 중에 업데이트된 VCL 코드 버전을 빠르게 확인합니다. 유효성 검사가 실패하면 사용자 지정 VCL 코드 조각을 편집하여 문제를 해결하십시오. 그런 다음 VCL을 다시 업로드합니다.

## 요청 차단을 위한 추가 VCL 예

다음 예에서는 ACL 목록 대신 인라인 조건 문을 사용하여 요청을 차단하는 방법을 보여 줍니다.

>[!WARNING]
>
>이러한 예에서 VCL 코드는 파일에 저장하고 Fastly API 요청에 제출할 수 있는 JSON 페이로드로 포맷됩니다. Fastly API를 사용하여 [VCL 코드 조각을 관리자](#add-the-custom-vcl-snippet)에서 제출하거나 JSON 문자열로 제출할 수 있습니다. JSON 문자열과 함께 Fastly API를 사용할 때 유효성 검사 오류를 방지하려면 백슬래시를 사용하여 특수 문자를 이스케이프 처리해야 합니다.

>[!NOTE]
>관리자에서 VCL 코드 조각을 제출하는 경우 샘플 VCL 코드에서 개별 값을 추출하여 해당 필드에 입력합니다. For example:
>- 이름: `<name of the VCL>`
>- 동적: `<0/1>`
>- 유형: `<type>`
>- 우선 순위: `<priority>`
>- 콘텐츠: `<content>`

Fastly VCL 설명서에서 [동적 VCL 조각 사용](https://docs.fastly.com/vcl/vcl-snippets/)을 참조하십시오.

### VCL 코드 샘플: 국가 코드별 차단

이 예제에서는 IP 주소와 연결된 국가에 대한 두 문자 ISO 3166-1 국가 코드를 사용합니다.

```json
{
  "name": "blockbycountrycode",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( client.geo.country_code == \"HK\" ) { error 405 \"Not allowed\";}"
}
```

>[!NOTE]
>
>사용자 지정 VCL 코드 조각을 사용하는 대신 Cloud Infrastructure Admin의 Adobe Commerce에서 Fastly [Blocking](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BLOCKING.md) 기능을 사용하여 국가 코드 또는 국가 코드 목록별 차단을 구성할 수 있습니다.

### VCL 코드 샘플: HTTP 사용자 에이전트 요청 헤더로 차단

```json
{
  "name": "blockbyuseragent",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( req.http.User-Agent ~ \"(UCBrowser|MQQBrowser|LieBaoFast|Mb2345Browser)\" ) {error 405 \"Not allowed\";}"
}
```

{{automate-vcl-snippet-deployment}}

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}

<!-- Last updated from includes: 2025-01-27 17:16:28 -->
