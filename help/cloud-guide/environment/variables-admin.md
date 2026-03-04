---
title: 관리자 변수
description: 클라우드 인프라에 Adobe Commerce을 설치할 때 사용되는 환경 변수 목록을 참조하십시오.
feature: Cloud, Configuration, Install, Roles/Permissions
role: Developer
exl-id: d2746185-bc59-4d30-a088-73df1bd2c0b2
source-git-commit: 4e751f02b92f954cd41d5523237da295a068661a
workflow-type: tm+mt
source-wordcount: '758'
ht-degree: 0%

---

# 관리자 변수

클라우드 인프라 프로젝트의 Adobe Commerce에 대한 관리 액세스 권한이 있는 사용자는 다음 프로젝트 환경 변수를 사용하여 관리 UI에 액세스하기 위한 관리 사용자 계정에 대한 구성 설정을 재정의할 수 있습니다.

## 관리자 자격 증명

다음 표의 ADMIN 변수를 사용하여 Commerce 설치 중에 관리자 사용자 자격 증명을 재정의할 수 있습니다.

설치 후 값을 변경하려면 SSH를 사용하여 환경에 연결한 다음 Adobe Commerce CLI [`admin:user` 명령](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/tutorials/admin.html)을 사용하여 관리자 자격 증명을 만들거나 편집하십시오.

| 변수 | 기본값 | 설명 |
| -------------- | --------------------------- | ----------- |
| `ADMIN_USERNAME` | 라이선스 소유자 이메일 주소 | 관리 사용자를 포함하여 다른 사용자를 생성할 수 있는 관리 사용자의 사용자 이름입니다. |
| `ADMIN_EMAIL` |                             | 관리자의 이메일 주소. 이 주소는 암호 재설정 알림을 보내는 데 사용됩니다. |
| `ADMIN_PASSWORD` |                             | 관리자 암호. 프로젝트가 생성되면 무작위 암호가 생성되고 이메일이 라이선스 소유자에게 전송됩니다. 프로젝트를 만드는 동안 라이선스 소유자가 이미 암호를 변경했어야 합니다. 업데이트된 암호는 라이센스 소유자에게 문의하십시오. |
| `ADMIN_LOCALE` | `en_US` | 관리자가 사용하는 기본 로케일. |

## 관리자 URL

관리 UI에 대한 액세스 보안을 유지하려면 다음 환경 변수를 사용하십시오. 지정하면 이 값이 설치 중에 기본 URL을 재정의합니다. 클라우드 인프라의 [!DNL Adobe Commerce]에서 ([!DNL Cloud Console] 또는 [!DNL Cloud CLI])의 `ADMIN_URL` 변수를 사용하여 관리 URL을 설정하거나 변경해야 합니다. [!DNL Admin]에서 설정을 수정하는 것은 온-프레미스 설치에만 적용할 수 있습니다.

`ADMIN_URL` - 관리 UI에 액세스할 상대 URL입니다. 기본 URL은 `/admin`입니다.

### 관리자 URL 변경

기본적으로 [Commerce 관리자](https://experienceleague.adobe.com/docs/commerce-admin/start/admin/admin.html) URL은 *&lt;domain_name>/admin*(으)로 설정됩니다. 보안상의 이유로 Adobe에서는 쉽게 추측할 수 없는 고유한 사용자 지정 관리자 URL로 변경할 것을 권장합니다.

**클라우드 인프라의 [!DNL Adobe Commerce]에서**&#x200B;의 `ADMIN_URL` 환경 변수를 사용하여 관리 URL을 변경해야 합니다([!DNL Cloud Console] 또는 [!DNL Cloud CLI]). [!DNL Admin]에서 설정을 수정하는 것은 온-프레미스 설치에만 적용할 수 있습니다. 온-프레미스 설치의 경우 [사용자 지정 관리자 URL을 사용](https://experienceleague.adobe.com/docs/commerce-admin/stores-sales/site-store/store-urls.html#use-a-custom-admin-url)하세요.

Adobe은 설치 후 관리 URL에 대한 환경 수준 변수를 변경할 것을 권장합니다. 복제된 `master` 환경에서 분기하기 전에 보안상의 이유로 이 설정을 구성하십시오. 상속을 false로 설정하지 않는 한 `master` 분기에서 만든 모든 분기는 환경 수준 변수와 해당 값을 상속합니다.

[!DNL Cloud Console] 또는 [!DNL Cloud CLI]을(를) 사용하여 `ADMIN_URL`을(를) 설정하거나 업데이트하십시오.

#### 옵션 A: [!DNL Cloud Console]을(를) 사용하여 관리자 URL 변경

##### 통합 환경

[클라우드 콘솔](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/project/overview.html)에서 다음을 사용하여 새 변수를 추가합니다.

- **이름:** `ADMIN_URL`
- **값:** 새 관리자 URL(예: `magento_A8v10`)

- 자세한 단계는 개발자 설명서에서 [환경 변수 추가](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/project/overview.html#configure-environment) 또는 [환경 변수 추가](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/env/stage/variables-admin.html)를 참조하십시오.

##### [!DNL Cloud Console]에서 관리자 URL 설정

1. [클라우드 콘솔](https://console.adobecommerce.com/)에 로그인합니다.
2. **[!UICONTROL All projects]** 목록에서 프로젝트를 선택하십시오.
3. 프로젝트 개요에서 환경을 선택하고 구성 아이콘을 클릭합니다.
4. **[!UICONTROL Variables]** 탭을 선택합니다.
5. **[!UICONTROL Create Variable]**&#x200B;을(를) 클릭하거나 기존 `ADMIN_URL` 변수가 있는 경우 편집합니다.
6. 다음을 입력합니다.
   - **변수 이름:** `ADMIN_URL`
   - **값:** 새 관리자 경로(예: `magento_A8v10`).

   기본적으로 **[!UICONTROL Available during runtime]** 및 **[!UICONTROL Make inheritable]**&#x200B;이(가) 선택됩니다. 하위 환경이 이 값을 상속하지 못하도록 하려면 이 변수에 대해 **[!UICONTROL Make inheritable]**&#x200B;을(를) 지웁니다.
7. **[!UICONTROL Create variable]**(또는 **[!UICONTROL Save]**)을(를) 클릭하고 배포가 완료될 때까지 기다립니다. 필수 필드에 값이 포함된 경우에만 단추가 표시됩니다.

##### [!DNL Cloud Console]에서 스테이징 및 프로덕션을 사용할 수 없는 경우

스테이징 또는 프로덕션 환경에 대한 `ADMIN_URL` 변수를 추가하도록 요청하는 [지원 티켓을 제출](https://experienceleague.adobe.com/en/docs/support-resources/adobe-support-tools-guide/adobe-commerce-support/adobe-commerce-help-center-user-guide#submit-ticket). [!DNL Cloud Console]에서 스테이징 및 프로덕션에 액세스할 수 있는 경우 [통합 환경](#integration-environment)에 설명된 대로 변수를 추가하십시오.

#### 옵션 B: [!DNL Cloud CLI]을(를) 사용하여 관리자 URL 변경

`magento-cloud variable:update` 명령을 사용하여 변수를 업데이트합니다. `variable:set` 명령은 더 이상 사용되지 않으며 사용할 수 없습니다.

다음 예제에서는 `master` 환경 `ADMIN_URL`을(를) `newAdmin_A8v10`(으)로 업데이트하여 하위 환경이 값을 상속하지 못하도록 합니다.

```bash
magento-cloud variable:update ADMIN_URL --value newAdmin_A8v10 -e master --inheritable false
```

- **재배포:** [!DNL Cloud CLI]에서 `ADMIN_URL` 변수를 변경하면 환경의 재배포가 트리거됩니다.
- **상속:** 변수는 기본적으로 상속할 수 있습니다. 값이 하위 환경에 상속되지 않도록 하려면 표시된 대로 `--inheritable false` 옵션을 사용합니다. 자세한 내용은 [변수 수준 가시성](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/env/variable-levels.html#visibility)을 참조하세요.

>[!NOTE]
>
>`ADMIN_URL` 값은 문자(a-z, A-Z), 숫자(0-9) 및 밑줄 문자(_)를 허용합니다. 공백이나 다른 문자는 사용할 수 없습니다.
