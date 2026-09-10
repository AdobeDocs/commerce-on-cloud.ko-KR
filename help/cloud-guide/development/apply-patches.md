---
title: 패치 적용
description: ECE-Tools 및 Quality Patches Tool을 사용하여 Adobe Commerce on Cloud Infrastructure 프로젝트에 필수, 선택적 및 사용자 지정 패치를 적용하는 방법을 알아봅니다.
feature: Cloud, Upgrade
exl-id: 923c1e43-45da-450f-bdfc-de84a901400d
TQID: https://experienceleague.adobe.com/SyS-AIRHp0LW7Z4JwZw2FNtbvy9FVzISUID12MjlMrc
product_v2: id: eadea719-cf89-469b-a6fd-a236a7138047
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bdid: ff6a42d2-313e-452e-93a6-792e4fad9ff8
source-git-commit: 8b6f9dbc2010ec0afe5904490a2f6d6a22ad2b39
workflow-type: tm+mt
source-wordcount: 922
ht-degree: 0%

---

# 패치 적용

`magento/magento-cloud-patches` 작성기 패키지([Commerce 릴리스 노트](../release-notes/cloud-patches.md)용 클라우드 패치 참조) 및 [품질 패치 도구](https://github.com/magento/quality-patches)는 설치된 Adobe Commerce 애플리케이션에 패치를 제공합니다.

- Commerce용 클라우드 패치 패키지는 중요한 수정 사항이 있는 필요한 패치를 제공합니다
- 품질 패치는 이전 버전과 호환되지 않는 변경 사항이 포함되지 않은 [개별 패치](https://experienceleague.adobe.com/en/docs/commerce-operations/release/planning/versioning-policy#individual-patch)와(과) 같이 영향이 적은 선택적 품질 수정 사항을 제공합니다.

릴리스된 패치의 전체 목록을 검토하려면 _Commerce 작업 도구 안내서_&#x200B;의 [사용 가능한 패치](https://experienceleague.adobe.com/en/tools/commerce-quality-patches)를 참조하십시오.

두 패키지 모두 클라우드 환경과 모든 Adobe Commerce 버전의 통합을 개선하고 중요, 선택 사항 및 사용자 지정 수정 사항의 빠른 전달을 지원합니다. 이 패키지를 사용하여 Commerce에 사용할 수 있는 모든 개별 패치에 대한 일반 정보를 적용, 되돌리기 및 볼 수 있습니다.

>[!TIP]
>
>[품질 패치 도구](https://experienceleague.adobe.com/en/tools/commerce-quality-patches) 및 Commerce용 클라우드 패치를 Magento Open Source 및 Adobe Commerce 프로젝트에 대한 독립 패키지로 사용할 수 있습니다. Adobe은 비클라우드 프로젝트에 품질 패치 도구를 사용하는 것을 권장합니다.

원격 환경에 변경 내용을 배포할 때 `ece-tools` 패키지는 `magento/magento-cloud-patches` 및 `magento/quality-patches`을(를) 사용하여 보류 중인 패치를 확인하고 다음 순서로 자동으로 적용합니다.

1. Commerce용 클라우드 패치 패키지에 포함된 모든 필수 Commerce 패치를 적용합니다.
1. 품질 패치 도구에 포함된 선택한 선택적 Commerce 패치를 적용합니다.
1. `/m2-hotfixes` 디렉터리에 패치 이름별로 알파벳순으로 사용자 지정 패치를 적용합니다.

>[!NOTE]
>
>`ece-tools` 또는 Commerce용 클라우드 패치를 업데이트하면 다음 배포 중에 최신 필수 패치가 적용됩니다. 또는 배포하기 전에 `ece-patches apply` CLI 명령을 사용하여 Cloud 환경에서 로컬로 패치를 적용하고 확인합니다. 배포 프로세스 중에는 필요한 패치를 건너뛸 수 없습니다.
>
>Adobe Commerce EE 권한이 있는 고객만 `repo.magento.com`의 Commerce 작성기 리포지토리에서 [Commerce용 클라우드 패치](../release-notes/cloud-patches.md)를 다운로드할 수 있습니다.

## 사전 요구 사항

{{upgrade-tip}}

품질 패치 도구는 Commerce 및 `ece-tools` 패키지용 클라우드 패치에 종속됩니다. 최신 패치를 적용하려면 [최신 버전의 ECE-Tools](../dev-tools/update-package.md)가 설치되어 있어야 합니다. ECE-Tools의 최소 필요 버전은 2002.1.2입니다.

## 사용 가능한 패치 및 상태 보기

사용 가능한 개별 패치 목록을 보려면 다음과 같이 하십시오.

```bash
php ./vendor/bin/ece-patches status
```

샘플 응답:

```
More detailed information about patches you can find on https://support.magento.com/
╔════════════════╤═════════════════════════════════════════════════╤══════════╤═════════════╤═════════════════════════════════╗
║ Id             │ Title                                           │ Type     │ Status      │ Details                         ║
╠════════════════╪═════════════════════════════════════════════════╪══════════╪═════════════╪═════════════════════════════════╣
║ MAGECLOUD-5069 │ FPC is getting disabled during deployments      │ Required │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-page-cache    ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MCLOUD-5650    │ Hold deployment config after reading from file  │ Required │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/framework            ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MCLOUD-5684    │ Pagination Not working - product_list_limit=all │ Required │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-elasticsearch ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-65837       │ Fix load balancer issue                         │Deprecated│ Applied     │ Recommended replacement: MC-1   ║
║                │                                                 │          │             │ Affected components:            ║
║                │                                                 │          │             │  - magento/framework            ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ BUNDLE-2554    │ Set Payment info bug                            │ Required │ Not applied │ Affected components:            ║
║                │                                                 │          │             │  - amzn/amazon-pay-module       ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-1           │ Fixes issue 1                                   │ Optional │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-cms           ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-2           │ Fixes issue 2                                   │ Optional │ Not applied │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-cms           ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-3           │ Fixes issue 3                                   │ Optional │ Not applied │ Required patches:               ║
║                │                                                 │          │             │  - MC-2                         ║
║                │                                                 │          │             │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-cms           ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ N/A            │ ../m2-hotfixes/MDVA_custom__2.3.5_ce.patch      │ Custom   │ N/A         │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-framework     ║
╚════════════════╧═════════════════════════════════════════════════╧══════════╧═════════════╧═════════════════════════════════╝
Magento 2 Enterprise Edition, version 2.3.5.0
```

상태 테이블에는 다음 유형의 정보가 포함되어 있습니다.

- **유형**:
  - `Optional` - 품질 패치 도구 및 클라우드 패치 패키지의 모든 패치는 Adobe Commerce 및 Magento Open Source 설치에 선택 사항입니다. 클라우드 인프라의 Adobe Commerce의 경우 모든 패치는 선택 사항입니다.
  - `Required` - Cloud Patches for Commerce 패키지의 모든 패치가 Cloud 고객에게 필요합니다.
  - `Deprecated` - 개별 패치가 더 이상 사용되지 않는 것으로 표시됩니다. Adobe은 적용한 경우 되돌리는 것을 권장합니다. 더 이상 사용되지 않는 패치를 되돌리면 상태 표에 더 이상 표시되지 않습니다.
  - `Custom`—`m2-hotfixes` 디렉터리의 모든 패치입니다.

- **상태**:
  - `Applied`—패치가 적용되었습니다.
  - `Not applied`—패치가 적용되지 않았습니다.
  - `N/A`—충돌로 인해 패치 상태를 정의할 수 없습니다.

- **세부 정보**:
  - `Affected components`—영향을 받는 모듈의 목록입니다.
  - `Required patches`—필요한 패치(종속성) 목록입니다.
  - `Recommended replacement`—더 이상 사용되지 않는 패치의 권장 대체 패치입니다.

## 로컬 환경에서 패치 적용

배포하기 전에 로컬 환경에서 패치를 수동으로 적용하고 테스트할 수 있습니다.

**로컬 개발 환경에서 개별 패치를 적용하려면**:

1. `QUALITY_PATCHES` 변수를 `.magento.env.yaml` 파일에 추가하고 아래에 필요한 패치를 나열합니다.

   ```yaml
   stage:
     build:
       QUALITY_PATCHES:
         - MCTEST-1002
         - MCTEST-1003
   ```

1. 프로젝트 루트에서 패치를 적용합니다.

   ```bash
   php ./vendor/bin/ece-patches apply
   ```

   `ece-patches apply` 명령은 다음 순서로 패치를 적용합니다.
   - 필수 패치
   - 개별 패치 옵션
   - `/m2-hotfixes` 디렉터리의 사용자 지정 패치

1. 캐시를 지웁니다.

   ```bash
   php ./bin/magento cache:clean
   ```

1. 패치를 테스트하고 사용자 지정 패치를 필요한 대로 변경합니다.

## 원격 환경에 패치 적용

>[!WARNING]
>
>Adobe은 프로덕션 환경에 배포하기 전에 통합 또는 스테이징 환경에서 모든 패치를 테스트할 것을 권장합니다.

**원격 환경에 패치를 적용하려면**:

1. `QUALITY_PATCHES` 변수를 `.magento.env.yaml` 파일에 추가하고 아래에 필요한 패치를 나열합니다.

   ```yaml
   stage:
     build:
       QUALITY_PATCHES:
         - MCTEST-1002
         - MCTEST-1003
   ```

   >[!NOTE]
   >
   >새 버전의 Adobe Commerce으로 업그레이드한 후 새 버전에 패치가 포함되지 않은 경우 패치를 다시 적용해야 합니다.

1. 업데이트된 `.magento.env.yaml` 파일을 추가, 커밋 및 푸시합니다.

   ```bash
   git add .magento.env.yaml
   ```

   ```bash
   git commit -m "Apply patch"
   ```

   ```bash
   git push origin <branch-name>
   ```

## 사용자 정의 패치 적용

배포할 때 ECE-Tools는 프로젝트 루트의 `/m2-hotfixes` 디렉터리에 추가하는 모든 Adobe 패치와 사용자 지정 패치를 적용합니다.

>[!NOTE]
>
>모든 패치 파일 이름은 `.patch` 확장명으로 끝나야 합니다.

**클라우드 환경에서 사용자 지정 패치를 적용하고 테스트하려면**:

1. 프로젝트 루트에 `m2-hotfixes`(이)라는 디렉터리가 없는 경우 만듭니다.

   ```bash
   mkdir m2-hotfixes
   ```

1. 패치 파일을 `/m2-hotfixes` 디렉터리에 복사합니다.

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add m2-hotfixes/
   ```

   ```bash
   git commit -m "Apply patch"
   ```

   ```bash
   git push origin <branch-name>
   ```

   >[!NOTE]
   >
   >프로덕션 이전 환경에서 모든 패치를 테스트해야 합니다. 클라우드 인프라의 Adobe Commerce의 경우 `magento-cloud environment:branch <branch-name>` CLI 명령을 사용하여 분기를 만들 수 있습니다.

## 사용자 정의 패치 되돌리기

이전에 적용된 사용자 지정 패치를 되돌리거나 제거하려면 다음 작업을 수행하십시오.

1. `/m2-hotfixes` 디렉터리에서 패치 파일을 삭제합니다.

1. 코드 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add m2-hotfixes/
   ```

   ```bash
   git commit -m "Revert patch"
   ```

   ```bash
   git push origin <branch-name>
   ```

   >[!NOTE]
   >
   >프로덕션 이전 환경에서 테스트해야 합니다. 클라우드 인프라의 Adobe Commerce의 경우 `magento-cloud environment:branch <branch-name>` CLI 명령을 사용하여 분기를 만들 수 있습니다.

## 비클라우드 프로젝트에 패치 적용

Magento Open Source 및 Adobe Commerce 프로젝트용 [품질 패치 도구](https://github.com/magento/quality-patches)를 사용하십시오.

## 로컬 환경에서 패치 되돌리기

`ece-patches` CLI를 사용하여 로컬 개발 환경에서 이전에 적용된 모든 패치를 되돌릴 수 있습니다.

적용된 모든 패치를 되돌리려면 다음을 수행합니다.

```bash
php ./vendor/bin/ece-patches revert
```

이 명령은 다음 순서로 모든 패치를 되돌립니다.

- /m2-hotfixes 디렉토리에서 적용된 모든 사용자 정의 패치를 되돌립니다.
- 적용된 모든 선택적 개별 패치를 되돌립니다.
- 적용된 모든 필수 패치를 되돌립니다.

## 로깅

품질 패치 도구는 모든 작업을 `<Project_root>/var/log/patch.log` 파일에 기록합니다.
