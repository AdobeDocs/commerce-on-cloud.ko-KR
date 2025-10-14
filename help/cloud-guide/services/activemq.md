---
title: ActiveMQ 서비스 설정
description: ActiveMQ Artemis 서비스를 사용하여 클라우드 인프라의 Adobe Commerce에 대한 메시지 대기열을 관리하는 방법을 알아봅니다.
feature: Cloud, Services
source-git-commit: ef22de6873b49f0fb9adfa9fc343a8d738a543e9
workflow-type: tm+mt
source-wordcount: '606'
ht-degree: 0%

---

# [!DNL ActiveMQ] 서비스 설정

[MQF(메시지 큐 프레임워크)](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/message-queues/message-queue-framework.html)은(는) [모듈](https://experienceleague.adobe.com/en/docs/commerce-operations/implementation-playbook/glossary#module)이(가) 메시지를 큐에 게시할 수 있도록 하는 Adobe Commerce 내의 시스템입니다. 또한 비동기적으로 메시지를 수신하는 소비자도 정의합니다.

MQF는 메시지를 보내고 받는 확장 가능한 플랫폼을 제공하는 메시징 브로커로 [ActiveMQ Artemis](https://activemq.apache.org/components/artemis/)을(를) 사용할 수 있습니다. 게재되지 않은 메시지를 저장하는 메커니즘도 포함됩니다. [!DNL ActiveMQ Artemis]은(는) 메시징을 위해 STOMP(Streaming Text Oriented Messaging Protocol) 프로토콜을 지원합니다.

[!DNL ActiveMQ Artemis]은(는) 메시지 큐 관리에 RabbitMQ를 대체할 수 있습니다. 이 기능은 ActiveMQ와 관련된 기능이 필요하거나 STOMP 프로토콜을 사용하려는 경우 특히 유용합니다.

>[!IMPORTANT]
>
>클라우드 인프라에서 Adobe Commerce을 사용하여 서비스를 만드는 대신 [!DNL ActiveMQ]과(와) 같은 기존 메시지 브로커 서비스를 사용하려는 경우 [`QUEUE_CONFIGURATION`](../environment/variables-deploy.md#queue_configuration) 환경 변수를 사용하여 사이트에 연결하십시오.

{{service-instruction}}

**ActiveMQ Artemis를 사용하려면**:

1. 설치된 ActiveMQ 버전과 함께 필요한 이름, 유형 및 디스크 값(MB)을 `.magento/services.yaml` 파일에 추가합니다.

   ```yaml
   activemq-artemis:
       type: activemq-artemis:<version>
       disk: 1024
   ```

1. `.magento.app.yaml` 파일에서 관계를 구성합니다.

   ```yaml
   relationships:
       activemq-artemis: "activemq-artemis:activemq-artemis"
   ```

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add .magento/services.yaml .magento.app.yaml
   ```

   ```bash
   git commit -m "Enable ActiveMQ Artemis service"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. [서비스 관계를 확인](services-yaml.md#service-relationships)합니다.

{{service-change-tip}}

## 디버깅을 위해 ActiveMQ에 연결

디버깅을 위해 다음 방법 중 하나로 서비스 인스턴스에 직접 연결할 수 있습니다.

- 로컬 개발 환경에서 연결
- 응용 프로그램에서 연결
- PHP 응용 프로그램에서 연결

### 로컬 개발 환경에서 연결

1. `magento-cloud` CLI 및 프로젝트에 로그인합니다.

   ```bash
   magento-cloud login
   ```

1. ActiveMQ Artemis가 설치 및 구성된 환경을 확인하십시오.

   ```bash
   magento-cloud environment:checkout <environment-id>
   ```

1. SSH를 사용하여 클라우드 환경에 연결합니다.

   ```bash
   magento-cloud ssh
   ```

1. [$MAGENTO_CLOUD_RELATIONSHIPS](../application/properties.md#relationships) 변수에서 ActiveMQ 연결 세부 정보 및 로그인 자격 증명을 검색합니다.

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   또는

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"])));'
   ```

   응답에서 ActiveMQ 정보를 찾습니다. 관계 이름은 `.magento.app.yaml` 파일에서 관계 이름을 구성한 방식에 따라 다릅니다. 예를 들어 `activemq-artemis`을(를) 관계 이름으로 사용한 경우:

   ```json
   {
      "activemq-artemis" : [
         {
            "password" : "guest",
            "ip" : "246.0.129.2",
            "scheme" : "stomp",
            "port" : 61616,
            "host" : "activemq-artemis.internal",
            "username" : "guest"
         }
      ]
   }
   ```

1. ActiveMQ로 로컬 포트 전달 사용(프로젝트가 US-3, EU-5 또는 AP-3 지역과 같은 다른 지역에 있는 경우 ``us-3``을(를) ``eu-5``/``ap-3``/``us``(으)로 대체)

   ```bash
   ssh -L <port-number>:activemq-artemis.internal:<port-number> <project-ID>-<branch-ID>@ssh.us.magentosite.cloud
   ```

   `http://localhost:8161`에서 ActiveMQ Artemis 웹 콘솔에 액세스하는 예는 다음과 같습니다.

   ```bash
   ssh -L 8161:activemq-artemis.internal:8161 <project-ID>-<branch-ID>@ssh.us.magentosite.cloud
   ```

   >[!NOTE]
   >
   >ActiveMQ Artemis는 STOMP 메시징에 포트 61616을 사용하고 웹 콘솔에 포트 8161을 사용합니다.

1. 세션이 열려 있는 동안에는 MAGENTO_CLOUD_RELATIONSHIPS 변수의 사용자 이름과 암호를 사용하여 `http://localhost:8161`의 ActiveMQ Artemis 웹 콘솔에 액세스할 수 있습니다.

### 응용 프로그램에서 연결

응용 프로그램에서 실행 중인 ActiveMQ에 연결하려면 PHP 응용 프로그램에 STOMP 클라이언트 라이브러리를 설치합니다.

### PHP 응용 프로그램에서 연결

PHP 응용 프로그램을 사용하여 ActiveMQ에 연결하려면 소스 트리에 PHP STOMP 라이브러리를 추가합니다. Adobe Commerce은 ActiveMQ 통신에 STOMP 프로토콜을 사용하며, ActiveMQ Artemis가 구성된 서비스로 검색되면 배포 중에 구성이 자동으로 설정됩니다.

## 프로토콜 지원

클라우드 인프라의 Adobe Commerce에 있는 ActiveMQ Artemis는 STOMP(Streaming Text Oriented Messaging Protocol) 프로토콜을 사용합니다.

- **STOMP**: 큐 작업에 사용되는 메시징 프로토콜(포트 61616)
- **웹 콘솔**: HTTP(포트 8161)를 통해 액세스할 수 있는 관리 인터페이스

## RabbitMQ와의 차이점

[!DNL ActiveMQ Artemis]과(와) [!DNL RabbitMQ]은(는) 모두 Adobe Commerce의 메시지 브로커 역할을 하지만 몇 가지 차이점이 있습니다.

- **프로토콜**: ActiveMQ Artemis는 STOMP 프로토콜을 사용하는 반면 RabbitMQ는 AMQP를 사용합니다.
- **구성**: ActiveMQ Artemis가 구성되면 Adobe Commerce에서 자동으로 STOMP 프로토콜을 사용합니다
- **우선 순위**: ActiveMQ와 RabbitMQ가 모두 구성된 경우 ActiveMQ가 STOMP 기반 작업에 우선하며 AMQP 작업은 RabbitMQ를 사용합니다.
- **웹 콘솔**: ActiveMQ는 큐 및 메시지 모니터링을 위한 웹 기반 관리 콘솔을 제공합니다

## 큐 구성

ActiveMQ Artemis가 서비스로 구성되면 Adobe Commerce은 STOMP 프로토콜을 사용하도록 큐 시스템을 자동으로 구성합니다. 배포 중에 `app/etc/env.php` 파일에 구성이 기록됩니다.

```php
'queue' => [
    'stomp' => [
        'host' => 'activemq-artemis.internal',
        'port' => '61616',
        'user' => 'guest',
        'password' => 'guest'
    ],
    'default_connection' => 'stomp'
]
```

필요한 경우 [`QUEUE_CONFIGURATION`](../environment/variables-deploy.md#queue_configuration) 환경 변수를 사용하여 이 구성을 재정의할 수 있습니다.

