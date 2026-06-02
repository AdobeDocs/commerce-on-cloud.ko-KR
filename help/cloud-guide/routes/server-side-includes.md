---
title: 서버측 포함
description: 클라우드 인프라에서 Adobe Commerce과 함께 서버측 포함을 사용하는 방법에 대해 알아봅니다.
feature: Cloud, Routes
exl-id: 826a9c9a-d082-4ec4-8fd2-00ca357522ab
TQID: https://experienceleague.adobe.com/iLal9p5QiG4U0sHrskzFV8buCCVWZAa3FLixND0Nw24
product_v2: id: eadea719-cf89-469b-a6fd-a236a7138047
feature_v2: id: dac87252-6066-4d6e-a9d2-f6d84c323de7
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bdid: ff6a42d2-313e-452e-93a6-792e4fad9ff8
source-git-commit: fd3ef8201c368f889344452e334976070a6c7157
workflow-type: tm+mt
source-wordcount: 182
ht-degree: 0%

---

# 서버측 포함

[Server-side includes](https://nginx.org/en/docs/http/ngx_http_ssi_module.html)&#x200B;(SSI)는 페이지가 렌더링되는 동안 서버에서 평가되는 HTML 페이지의 지시문입니다. SSI를 사용하면 전체 페이지를 제공하지 않고 동적으로 생성된 콘텐츠를 기존 HTML 페이지에 추가할 수 있습니다.

`.magento/routes.yaml`에서 경로별로 SSI를 활성화하거나 비활성화할 수 있습니다. 예:

```yaml
    "http://{default}/":
        type: upstream
        upstream: "myapp:php"
        cache:
            enabled: false
            ssi:
                enabled: true
    "http://{default}/time.php":
        type: upstream
        upstream: "myapp:php"
        cache:
            enabled: true
```

SSI를 사용하면 기존 [캐싱 구성](caching.md)을 고려하여 서버가 HTML의 일부를 채우도록 하는 HTML 응답 지시문에 을 포함할 수 있습니다.

다음 예제에서는 동적 날짜 컨트롤을 페이지 맨 위에 삽입하고 600초마다 업데이트되는 다른 날짜 컨트롤을 맨 아래에 삽입하는 방법을 보여 줍니다.

`/index.php`과(와) 같은 페이지에 다음 내용을 추가하십시오.

```php?start_inline=1
echo date(DATE_RFC2822);
<!--#include virtual="time.php" -->
```

`time.php`에 다음 추가:

```php?start_inline=1
header("Cache-Control: max-age=600");
echo date(DATE_RFC2822);
```

컨트롤을 추가한 페이지로 이동합니다. 페이지를 여러 번 새로 고치고 페이지 맨 위의 시간은 변경되지만 맨 아래의 시간은 600초마다 변경됩니다.
