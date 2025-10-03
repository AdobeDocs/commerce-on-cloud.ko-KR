---
title: Commerce 버전 업그레이드
description: 클라우드 인프라 환경에서 Adobe Commerce 버전을 업그레이드하는 방법을 알아봅니다.
feature: Cloud, Upgrade
exl-id: 0cc070cf-ab25-4269-b18c-b2680b895c17
source-git-commit: 7f9aac358effdf200b59678098e6a1635612301b
workflow-type: tm+mt
source-wordcount: '898'
ht-degree: 0%

---

# Commerce 버전 업그레이드

Adobe Commerce 코드 베이스를 최신 버전으로 업그레이드할 수 있습니다. 환경을 업그레이드하기 전에 [설치](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) 안내서의 _시스템 요구 사항_&#x200B;에서 최신 소프트웨어 버전 요구 사항을 검토하십시오.

환경 유형(개발, 스테이징 또는 프로덕션)에 따라 업그레이드 작업에는 다음이 포함될 수 있습니다.

- 새 Adobe Commerce 버전과의 호환성을 위해 MariaDB(MySQL), OpenSearch, RabbitMQ 및 Redis의 새 버전으로 `.magento/services.yaml` 파일을 업데이트하십시오.
- 후크 및 환경 변수에 대한 새로운 설정으로 `.magento.app.yaml` 파일을 업데이트합니다.
- 타사 확장을 지원되는 최신 버전으로 업그레이드하십시오.

{{upgrade-tip}}

{{pro-update-service}}

## 구성 파일

애플리케이션을 업그레이드하기 전에 클라우드 인프라 또는 애플리케이션에서 Adobe Commerce의 기본 구성 설정을 변경하기 위해 프로젝트 구성 파일을 업데이트해야 합니다. 최신 기본값은 [magento-cloud GitHub 저장소](https://github.com/magento/magento-cloud)에서 찾을 수 있습니다.

### composer.json

업그레이드하기 전에 항상 `composer.json` 파일의 종속성이 Adobe Commerce 버전과 호환되는지 확인하십시오.

Adobe Commerce 버전 2.4.4 이상의 `composer.json` 파일을 업데이트하려면 다음을 **.

1. `allow-plugins` 섹션에 다음 `config`을(를) 추가합니다.

   ```json
   "config": {
      "allow-plugins": {
         "dealerdirect/phpcodesniffer-composer-installer": true,
         "laminas/laminas-dependency-plugin": true,
         "magento/*": true
      }
   },
   ```

1. `require` 섹션에 다음 플러그인을 추가하십시오.

   ```json
   "require": {
       "magento/composer-root-update-plugin": "^2.0.3"
   },
   ```

1. `extra:component_paths` 섹션에 다음 구성 요소를 추가합니다.

   ```json
   "extra": {
      "component_paths": {
         "tinymce/tinymce": "lib/web/tiny_mce_5"
      },
   },
   ```

1. 파일을 저장합니다. 아직 분기에 변경 사항을 커밋하거나 푸시하지 마십시오.

1. 업그레이드 프로세스를 계속합니다.

## 환경 백업

업그레이드 전에 인스턴스의 백업을 만드는 것이 좋습니다. 다음 단계를 사용하여 통합, 스테이징 및 프로덕션 환경을 백업합니다.

**통합 환경 데이터베이스 및 코드를 백업하려면**:

1. 원격 데이터베이스의 로컬 백업을 만듭니다.

   ```bash
   magento-cloud db:dump
   ```

   >[!NOTE]
   >
   >`magento-cloud db:dump` 명령은 테이블을 잠그지 않고 데이터베이스를 백업할 수 있도록 [&#x200B; 플래그를 사용하여 &#x200B;](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)mysqldump`--single-transaction` 명령을 실행합니다.

1. 코드 및 미디어를 백업합니다.

   ```bash
   php bin/magento setup:backup --code [--media]
   ```

   소스 제어에 이미 많은 정적 파일이 있는 경우 `[--media]`을(를) 생략할 수도 있습니다.

**배포 전에 스테이징 또는 프로덕션 환경 데이터베이스를 백업하려면**:

1. SSH를 사용하여 원격 환경에 로그인합니다.

1. [데이터베이스 덤프](../storage/database-dump.md)를 만듭니다. DB 덤프의 대상 디렉터리를 선택하려면 `--dump-directory` 옵션을 사용합니다.

   ```bash
   vendor/bin/ece-tools db-dump
   ```

   덤프 작업에서 원격 프로젝트 디렉터리에 `dump-<timestamp>.sql.gz` 보관 파일을 만듭니다. [데이터베이스 백업](../storage/database-dump.md)을 참조하십시오.

## 애플리케이션 업그레이드

응용 프로그램을 업그레이드하기 전에 [서비스 버전](../services/services-yaml.md#service-versions) 정보에서 최신 소프트웨어 버전 요구 사항을 검토하십시오.

**응용 프로그램 버전을 업그레이드하려면**:

1. 로컬 워크스테이션에서 프로젝트 디렉터리로 변경합니다.

1. 대상 업그레이드 버전에 대해 [버전 제약 조건](overview.md#cloud-metapackage)을 설정하십시오. 이 단계는 대상 버전이 기존 제약 조건을 벗어나는 경우에만 필요합니다.

   ```bash
   composer require-commerce "magento/magento-cloud-metapackage":">=CURRENT_VERSION <NEXT_VERSION" --no-update
   ```

   >[!NOTE]
   >
   >`ece-tools` 패키지를 업데이트하려면 버전 제약 조건 구문을 사용해야 합니다. `composer.json` 파일에서 업그레이드에 사용하는 [응용 프로그램 템플릿](https://github.com/magento/magento-cloud/blob/master/composer.json) 버전에 대한 버전 제약 조건을 찾을 수 있습니다.

1. `composer.json` 파일을 핵심 Commerce 업그레이드 버전으로 업데이트하십시오.

   ```bash
   composer require-commerce magento/product-enterprise-edition 2.4.8 --no-update
   ```

1. B2B를 사용하는 경우 `composer.json` 파일을 Commerce용 [지원되는 버전](https://experienceleague.adobe.com/en/docs/commerce-operations/release/product-availability#adobe-authored-extensions)&#x200B;(으)로 업데이트하십시오.

   ```bash
   composer require-commerce magento/extension-b2b 1.5.2 --no-update
   ```

1. 프로젝트 종속성을 업데이트합니다.

   ```bash
   composer update
   ```

1. 현재 적용된 패치를 검토합니다.

   - `m2-hotfixes` 디렉터리에 패치가 설치되어 있는 경우 [Adobe Commerce 지원 티켓을 제출](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#support-case)하고 Adobe Commerce 지원 팀과 함께 새 버전에 적용할 수 있는 패치를 확인하십시오. `m2-hotfixes` 디렉터리에서 적용할 수 없는 패치를 제거합니다.

   - [ 파일에 ]품질 패치`.magento.env.yaml`가 적용된 경우 새 버전에 계속 적용할 수 있는지 확인하십시오. `QUALITY_PATCHES` 파일의 `.magento.env.yaml` 섹션에서 적용할 수 없는 패치를 제거합니다.

   **메서드 1**: [품질 패치 릴리스 정보에서 해당 버전을 확인](https://experienceleague.adobe.com/en/docs/commerce-operations/tools/quality-patches-tool/release-notes)

   **메서드 2**: [사용 가능한 패치와 상태 보기](https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/develop/upgrade/apply-patches#view-available-patches-and-status)

   **메서드 3**: [패치 검색](https://experienceleague.adobe.com/tools/commerce-quality-patches/index.html?lang=en)


1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Upgrade"
   ```

   ```bash
   git push origin <branch-name>
   ```

   Composer가 기본 패키지를 마샬링하는 방식 때문에 변경된 모든 파일을 소스 제어에 추가하려면 `git add -A`이(가) 필요합니다. 기본 패키지(`composer install` 및 `composer update`)의 `magento/magento2-base` 및 `magento/magento2-ee-base` 파일을 모두 패키지 루트로 마샬링합니다.

   작성기가 마샬링하는 파일은 새 버전의 Adobe Commerce에 속하므로 동일한 파일의 오래된 버전을 덮어씁니다. 현재 Adobe Commerce에서는 마샬링을 사용할 수 없으므로 마샬링된 파일을 소스 제어에 추가해야 합니다.

1. 배포가 완료될 때까지 기다립니다.

1. SSH를 사용하여 로그인하고 버전을 확인하여 통합, 스테이징 또는 프로덕션 환경에서 업그레이드를 확인합니다.

   ```bash
   php bin/magento --version
   ```

### 확장 업그레이드

Marketplace 또는 기타 회사 사이트에서 타사 확장 및 모듈 페이지를 검토하고 클라우드 인프라에서 Adobe Commerce 및 Adobe Commerce에 대한 지원을 확인하십시오. 타사 확장 및 모듈을 업그레이드해야 하는 경우 Adobe에서는 확장이 비활성화된 새 통합 분기에서 작업하는 것이 좋습니다.

**확장을 확인하고 업그레이드하려면**:

1. 로컬 워크스테이션에 분기를 만듭니다.

1. 필요에 따라 확장을 비활성화합니다.

1. 사용 가능한 경우 확장 업그레이드를 다운로드합니다.

1. 타사 설명서에 따라 업그레이드를 설치합니다.

1. 확장을 활성화하고 테스트합니다.

1. 코드 변경 사항을 원격에 추가, 커밋 및 푸시합니다.

1. 을 푸시하고 통합 환경에서 테스트합니다.

1. 스테이징 환경으로 푸시하여 사전 프로덕션 환경에서 테스트합니다.

Adobe에서는 사이트 실행 프로세스에서 업그레이드된 확장을 포함하여 프로덕션 환경을 _before_ 업그레이드하는 것이 좋습니다.

>[!NOTE]
>
>응용 프로그램 버전을 업그레이드하면 업그레이드 프로세스가 [Fastly CDN 모듈](../cdn/fastly.md#fastly-cdn-module-for-magento-2)의 최신 버전으로 자동으로 업데이트됩니다.

## 업그레이드 문제 해결

업그레이드가 실패하는 경우 브라우저에 상점 또는 관리 패널에 액세스할 수 없다는 오류 메시지가 표시됩니다.

```
There has been an error processing your request
Exception printing is disabled by default for security reasons.
  Error log record number: <error-number>
```

**오류를 해결하려면**:

1. 로컬 워크스테이션에서 프로젝트 디렉터리로 변경합니다.

1. SSH를 사용하여 원격 환경에 로그인합니다.

   ```bash
   magento-cloud ssh
   ```

1. `./app/var/report/<error number>` 파일을 엽니다.

1. [로그를 검사하고](../test/log-locations.md) 문제의 원인을 확인합니다.

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add -A && git commit -m "Fixed deployment failure" && git push origin <branch-name>
   ```
