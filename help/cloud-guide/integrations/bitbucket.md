---
title: Bitbucket 통합
description: Adobe Commerce on cloud infrastructure 프로젝트를 Bitbucket과 통합하는 방법에 대해 알아봅니다.
feature: Cloud, Integration
source-git-commit: 1e789247c12009908eabb6039d951acbdfcc9263
workflow-type: tm+mt
source-wordcount: '1020'
ht-degree: 0%

---

# Bitbucket 통합

코드 변경 사항을 푸시할 때 환경을 자동으로 빌드하고 배포하도록 Bitbucket 저장소를 구성할 수 있습니다. 이 통합은 Bitbucket 저장소를 클라우드 인프라 계정의 Adobe Commerce과 동기화합니다.

{{private-repository}}

## 전제 조건

- Adobe Commerce on cloud infrastructure 프로젝트에 대한 관리자 액세스
- 로컬 환경의 [`magento-cloud` CLI](../dev-tools/cloud-cli-overview.md) 도구
- Bitbucket 계정
- Bitbucket 저장소에 대한 관리자 액세스
- Bitbucket 저장소에 대한 SSH 액세스 키

## 저장소 준비

기존 환경에서 Adobe Commerce on cloud infrastructure 프로젝트를 복제하고 프로젝트 분기를 새로운 빈 Bitbucket 저장소로 마이그레이션하여 동일한 분기 이름을 유지합니다. Adobe Commerce on cloud infrastructure 프로젝트에서 기존 환경 또는 분기를 잃지 않도록 동일한 Git 트리를 유지하는 것은 **위험**&#x200B;입니다.

1. 터미널에서 Adobe Commerce on cloud infrastructure 프로젝트에 로그인합니다.

   ```bash
   magento-cloud login
   ```

1. 프로젝트 나열 및 프로젝트 ID 복사

   ```bash
   magento-cloud project:list
   ```

1. 프로젝트를 로컬 환경에 복제합니다.

   ```bash
   magento-cloud project:get <project-ID>
   ```

1. Bitbucket 저장소를 원격으로 추가합니다.

   ```bash
   git remote add origin git@bitbucket.org:<user-name>/<repo-name>.git
   ```

   원격 연결의 기본 이름은 `origin` 또는 `magento`일 수 있습니다. `origin`이(가) 있으면 다른 이름을 선택하거나 기존 참조의 이름을 바꾸거나 삭제할 수 있습니다. [git-remote 설명서](https://git-scm.com/docs/git-remote)를 참조하세요.

1. Bitbucket 원격을 올바르게 추가했는지 확인합니다.

   ```bash
   git remote -v
   ```

   예상 응답:

   ```
   origin git@bitbucket.org:<user-name>/<repo-name>.git (fetch)
   origin git@bitbucket.org:<user-name>/<repo-name>.git (push)
   ```

1. 프로젝트 파일을 새 Bitbucket 저장소에 푸시합니다. 모든 분기 이름을 동일하게 유지하는 것을 잊지 마십시오.

   ```bash
   git push -u origin master
   ```

   새 Bitbucket 리포지토리로 시작하는 경우 원격 리포지토리가 로컬 복사본과 일치하지 않으므로 `-f` 옵션을 사용해야 할 수 있습니다.

1. Bitbucket 저장소에 모든 프로젝트 파일이 포함되어 있는지 확인합니다.

## OAuth 소비자 만들기

Bitbucket 통합에는 [OAuth 소비자](https://support.atlassian.com/bitbucket-cloud/docs/use-oauth-on-bitbucket-cloud/)가 필요합니다. 다음 섹션을 완료하려면 이 소비자의 OAuth `key` 및 `secret`이(가) 필요합니다.

**Bitbucket에서 OAuth 소비자를 만들려면**:

1. [Bitbucket](https://id.atlassian.com/login) 계정에 로그인합니다.

1. **설정** > **액세스 관리** > **OAuth**&#x200B;를 클릭합니다.

1. **소비자 추가**&#x200B;를 클릭하고 다음과 같이 구성하십시오.

   ![Bitbucket OAuth 소비자 구성](../../assets/oauth-consumer-config.png)

   >[!WARNING]
   >
   >올바른 **콜백 URL**&#x200B;이(가) 필요하지 않지만, 통합을 성공적으로 완료하려면 이 필드에 값을 입력해야 합니다.

1. **저장**&#x200B;을 클릭합니다.

1. 소비자 **이름**&#x200B;을(를) 클릭하여 OAuth `key` 및 `secret`을(를) 표시합니다.

1. 통합을 구성하기 위해 OAuth `key` 및 `secret`을(를) 복사합니다.

## 통합 구성

1. 터미널에서 Adobe Commerce on cloud infrastructure 프로젝트로 이동합니다.

1. `bitbucket.json`(이)라는 임시 파일을 만들고 다음 항목을 추가하여 꺾쇠 괄호 안의 변수를 값으로 바꿉니다.

   ```json
   {
     "type": "bitbucket",
     "repository": "<bitbucket-user-name/bitbucket-repo-name>",
     "app_credentials": {
       "key": "<oauth-consumer-key>",
       "secret": "<oauth-consumer-secret>"
     },
     "prune_branches": true,
     "fetch_branches": true,
     "build_pull_requests": true,
     "resync_pull_requests": true
   }
   ```

   >[!TIP]
   >
   >URL이 아닌 Bitbucket 저장소의 이름을 사용해야 합니다. URL을 사용하는 경우 통합이 실패합니다.

1. `magento-cloud` CLI 도구를 사용하여 프로젝트에 통합을 추가합니다.

   >[!WARNING]
   >
   >다음 명령은 Bitbucket 저장소의 코드로 Adobe Commerce on cloud infrastructure 프로젝트의 _all_ 코드를 덮어씁니다. 여기에는 `production` 분기를 포함한 모든 분기가 포함됩니다. 이 작업은 즉시 수행되며 실행 취소할 수 없습니다. 클라우드 인프라 프로젝트에서 Adobe Commerce의 모든 분기를 복제하여 Bitbucket 통합을 추가하기 **전**&#x200B;에 Bitbucket 저장소로 푸시하는 것이 가장 좋습니다.

   ```bash
   magento-cloud project:curl -p '<project-ID>' /integrations -i -X POST -d "$(< bitbucket.json)"
   ```

   헤더가 있는 긴 HTTP 응답을 반환합니다. 성공적인 통합은 200 또는 201 상태 코드를 반환합니다. 상태가 400 이상이면 오류가 발생했음을 나타냅니다.

1. 임시 `bitbucket.json` 파일을 삭제합니다.

1. 프로젝트 통합을 확인합니다.

   ```bash
   magento-cloud integrations -p <project-ID>
   ```

   ```
   +----------+-----------+--------------------------------------------------------------------------------+
   | ID       | Type      | Summary                                                                        |
   +----------+-----------+--------------------------------------------------------------------------------+
   | <int-id> | bitbucket | Repository: bitbucket_Account/magento-int                                      |
   |          |           | Hook URL:                                                                      |
   |          |           | https://magento-url.cloud/api/projects/<project-id>/integrations/<int-id>/hook |
   +----------+-----------+--------------------------------------------------------------------------------+
   ```

   BitBucket에서 웹후크를 구성하려면 **후크 URL**&#x200B;을 메모하십시오.

### BitBucket에 웹후크 추가

푸시 등의 이벤트를 Cloud Git 서버와 통신하려면 BitBucket 저장소에 대한 웹후크가 있어야 합니다. 이 페이지에 설명된 Bitbucket 통합 설정 방법을 올바르게 수행하면 자동으로 웹후크가 생성됩니다. 여러 통합을 생성하지 않도록 Webhook을 확인하는 것이 중요합니다.

1. [Bitbucket](https://id.atlassian.com/login) 계정에 로그인합니다.

1. **저장소**&#x200B;를 클릭하고 프로젝트를 선택합니다.

1. **저장소 설정** > **워크플로** > **웹후크**&#x200B;를 클릭합니다.

1. 계속하기 전에 웹후크를 확인하십시오.

   후크가 활성 상태인 경우 나머지 단계를 건너뛰고 [통합을 테스트](#test-the-integration)합니다. 후크는 **&quot;클라우드 인프라 &lt;project_id>&quot;**&#x200B;의 Adobe Commerce과 유사한 이름과 `https://<zone>.magento.cloud/api/projects/<project_id>/integrations/<id>/hook`과(와) 유사한 후크 URL 형식을 가져야 합니다.

1. **Webhook 추가**&#x200B;를 클릭합니다.

1. _새 웹후크 추가_ 보기에서 다음 필드를 편집합니다.

   - **제목**: Adobe Commerce 통합
   - **URL**: `magento-cloud` 통합 목록의 후크 URL을 사용합니다.
   - **트리거**: 기본값은 기본 _리포지토리 푸시_&#x200B;입니다.

1. **저장**&#x200B;을 클릭합니다.

### 통합 테스트

Bitbucket 통합을 구성한 후 `magento-cloud` CLI를 사용하여 통합이 작동하는지 확인할 수 있습니다.

```bash
magento-cloud integration:validate
```

또는 간단한 변경 사항을 Bitbucket 저장소에 푸시하여 테스트할 수 있습니다.

1. 테스트 파일을 만듭니다.

   ```bash
   touch test.md
   ```

1. 변경 사항을 커밋하고 Bitbucket 저장소에 푸시합니다.

   ```bash
   git add . && git commit -m "Testing Bitbucket integration" && git push
   ```

1. [[!DNL Cloud Console]](../project/overview.md)에 로그인하고 커밋 메시지가 표시되고 프로젝트가 배포되는지 확인합니다.

   ![Bitbucket 통합 테스트](../../assets/bitbucket-integration.png)

## 클라우드 분기 만들기

Bitbucket 통합은 Adobe Commerce on cloud infrastructure 프로젝트에서 새 환경을 활성화할 수 없습니다. Bitbucket으로 환경을 만드는 경우 환경을 수동으로 활성화해야 합니다. 이 추가 단계를 방지하려면 `magento-cloud` CLI 도구 또는 [!DNL Cloud Console]을(를) 사용하여 환경을 만드는 것이 좋습니다.

**Bitbucket으로 만든 분기를 활성화하려면**:

1. `magento-cloud` CLI를 사용하여 분기를 푸시합니다.

   ```bash
   magento-cloud environment:push from-bitbucket
   ```

   ```
   Pushing from-bitbucket to the new environment from-bitbucket
   Activate from-bitbucket after pushing? [Y/n] y
   Parent environment [master]: integration
   --- (Validation and activation messages)
   ```

1. 환경이 활성 상태인지 확인합니다.

   ```bash
   magento-cloud environment:list
   ```

   ```
   Your environments are:
   +---------------------+----------------+--------+
   | ID                  | Name           | Status |
   +---------------------+----------------+--------+
   | master              | Master         | Active |
   |  integration        | integration    | Active |
   |    from-bitbucket * | from-bitbucket | Active |
   +---------------------+----------------+--------+
   * - Indicates the current environment
   ```

환경을 만든 후 일반 Git 명령을 사용하여 원격 Bitbucket 저장소에 해당 분기를 푸시할 수 있습니다. 이후 Bitbucket의 분기를 변경하면 환경이 자동으로 빌드 및 배포됩니다.

## 통합 제거

코드에 영향을 주지 않고 프로젝트에서 Bitbucket 통합을 안전하게 제거할 수 있습니다.

**Bitbucket 통합을 제거하려면**:

1. 터미널에서 Adobe Commerce on cloud infrastructure 프로젝트에 로그인합니다.

1. 통합을 나열합니다. 다음 단계를 완료하려면 Bitbucket 통합 ID가 필요합니다.

   ```bash
   magento-cloud integration:list
   ```

1. 통합을 삭제합니다.

   ```bash
   magento-cloud integration:delete <int-ID>
   ```

또한 Bitbucket 계정에 로그인하고 계정 _설정_ 페이지에서 OAuth 부여를 취소하여 Bitbucket 통합을 제거할 수 있습니다.

## Bitbucket 서버 통합

Bitbucket 서버 통합을 사용하려면 다음이 필요합니다.

- [Bitbucket 액세스 토큰](https://confluence.atlassian.com/bitbucketserver/http-access-tokens-939515499.html)—프로젝트 `read` 액세스 및 저장소 `admin` 액세스 권한을 부여하는 토큰을 생성합니다.
- [Bitbucket 서버 URL](https://confluence.atlassian.com/bitbucketserver/specify-the-bitbucket-base-url-776640392.html)—Bitbucket 인스턴스의 기본 URL을 추가합니다.

Cloud CLI를 사용하여 Bitbucket 서버 통합 단계를 수행할 수 있지만 전체 명령은 다음과 유사합니다.

```bash
magento-cloud integration:add --type=bitbucket_server --base-url=<bitbucket-url> --username=<username> --token=<bitbucket-access-token> --project=<project-ID>
```

추가 사용 요구 사항 및 옵션을 보려면 help 명령을 사용하십시오. `magento-cloud integration:add --help`
