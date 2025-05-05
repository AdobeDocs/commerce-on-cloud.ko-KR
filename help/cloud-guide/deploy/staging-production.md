---
title: 스테이징 및 프로덕션에 배포
description: 추가 테스트를 위해 클라우드 인프라 코드에서 Adobe Commerce을 스테이징 및 프로덕션 환경에 배포하는 방법에 대해 알아봅니다.
feature: Cloud, Console, Deploy, SCD, Storage
source-git-commit: 1e789247c12009908eabb6039d951acbdfcc9263
workflow-type: tm+mt
source-wordcount: '1310'
ht-degree: 0%

---

# 스테이징 및 프로덕션에 배포

배포 및 라이브 진행 프로세스는 개발에서 시작하여 스테이징으로 이어지고 프로덕션에서 라이브로 끝납니다. Adobe은 일관된 구성을 보장하는 완벽한 환경 솔루션을 제공합니다. 모든 환경에서는 Storefront 및 Admin에 대한 직접 URL 액세스와 CLI 명령에 대한 SSH 액세스를 지원합니다.

스토어를 배포할 준비가 되면 프로덕션에 배포하기 전에 스테이징 환경에서 배포 및 테스트를 완료해야 합니다. 이 섹션에서는 빌드 및 배포 프로세스, 데이터 및 콘텐츠 마이그레이션, 테스트에 대한 심층적인 지침과 정보를 제공합니다.

>[!TIP]
>
>Adobe은 배포하기 전에 환경의 [백업](../storage/snapshots.md)을(를) 만들 것을 권장합니다.

또한 [New Relic을 사용하여 배포 추적](../monitor/track-deployments.md)을 사용하면 배포 이벤트를 모니터링하고 배포 간 성능을 분석할 수 있습니다.

## 스타터 배포 플로우

Adobe은 스타터 계획 개발 및 배포를 가장 잘 지원하기 위해 `master` 분기에서 `staging` 분기를 만들 것을 권장합니다. 그러면 네 개의 활성 환경 중 두 개를 준비했습니다. 프로덕션용 `master` 및 스테이징용 `staging`.

프로세스에 대한 자세한 내용은 [Starter Develop and Deploy Workflow](../architecture/starter-develop-deploy-workflow.md)를 참조하십시오.

## Pro 배포 플로우

Pro에는 두 개의 활성 분기, 전역 `master` 분기, 스테이징 및 프로덕션 분기가 있는 대규모 통합 환경이 포함되어 있습니다. 프로젝트를 만들 때 코드를 사이트 빌드 및 배포를 분기, 개발 및 추진할 준비가 되었습니다. 통합 환경에는 여러 분기가 있을 수 있지만 스테이징 및 프로덕션 환경에는 각 환경에 대해 하나의 분기만 있습니다.

프로세스에 대한 자세한 내용은 [Pro 워크플로우 개발 및 배포](../architecture/pro-develop-deploy-workflow.md)를 참조하십시오.

## 스테이징에 코드 배포

스테이징 환경은 데이터베이스, 웹 서버 및 Fastly와 New Relic을 포함한 모든 서비스를 포함하는 프로덕션에 가까운 환경을 제공합니다. 터미널 응용 프로그램을 통해 [[!DNL Cloud Console]](../project/overview.md) 또는 [Cloud CLI 명령](../dev-tools/cloud-cli-overview.md)을 통해 완전히 푸시, 병합 및 배포할 수 있습니다.

### [!DNL Cloud Console] (으)로 코드 배포

[!DNL Cloud Console]은(는) 시작 및 Pro 계획을 위한 통합, 스테이징 및 프로덕션 환경에서 코드를 만들고 관리하고 배포하는 기능을 제공합니다.

**Pro 프로젝트의 경우 통합 분기를 스테이징에 배포**:

1. 프로젝트에 [로그인](https://accounts.magento.cloud)합니다.
1. `integration` 분기를 선택하십시오.
1. 스테이징에 배포하려면 **병합** 옵션을 선택하십시오.

   ![병합](../../assets/button-merge.png){width="150"}

1. 스테이징 환경에서 모든 [테스트](../test/staging-and-production.md)를 완료합니다.
1. 스테이징이 준비되면 **병합** 옵션을 선택하여 프로덕션에 배포합니다.

**Starter의 경우 스테이징에 개발 분기 배포**:

1. 프로젝트에 [로그인](https://accounts.magento.cloud)합니다.
1. 준비된 코드 분기를 선택합니다.
1. 스테이징에 배포하려면 **병합** 옵션을 선택하십시오.

   ![병합](../../assets/button-merge.png){width="150"}

1. 스테이징 환경에서 모든 [테스트](../test/staging-and-production.md)를 완료합니다.
1. 스테이징이 준비되면 **병합** 옵션을 선택하여 프로덕션(`master`)에 배포합니다.

### 명령줄로 코드 배포

Cloud CLI는 코드를 배포하는 명령을 제공합니다. 프로젝트에 대한 SSH 및 Git 액세스 권한이 필요합니다.

#### 1단계: 통합 환경 배포 및 테스트

1. 프로젝트에 로그인한 후 통합 환경을 확인합니다.

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

1. 로컬 통합 환경을 원격 환경과 동기화:

   ```bash
   magento-cloud environment:synchronize <environment-ID>
   ```

1. 환경의 스냅샷을 백업으로 만듭니다.

   ```bash
   magento-cloud snapshot: create -e <environment-ID>
   ```

1. 필요에 따라 로컬 분기의 코드를 업데이트합니다.

1. 환경에 변경 사항을 추가, 커밋 및 푸시합니다.

   ```bash
   git add -A && git commit -m "Commit message" && git push origin <environment-ID>
   ```

1. 사이트 테스트를 완료합니다.

#### 2단계: 스테이징에 대한 변경 사항 병합 및 배포

1. 스테이징 환경을 확인합니다.

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

1. 로컬 스테이징 환경을 원격 환경과 동기화합니다.

   ```bash
   magento-cloud environment:synchronize <environment-ID>
   ```

1. 환경의 스냅샷을 백업으로 만듭니다.

   ```bash
   magento-cloud snapshot: create -e <environment-ID>
   ```

1. 배포할 스테이징에 통합 환경을 병합합니다.

   ```bash
   magento-cloud environment:merge <integration-ID>
   ```

1. 사이트 테스트를 완료합니다.

#### 3단계: 프로덕션에 배포

1. 로컬 프로덕션 환경의 스냅샷을 체크 아웃, 동기화 및 생성합니다.

1. 스테이징 환경을 프로덕션에 병합하여 배포합니다.

   ```bash
   magento-cloud environment:merge <staging-ID>
   ```

1. 사이트 테스트를 완료합니다.

## 정적 파일 마이그레이션

[정적 파일](https://experienceleague.adobe.com/ko/docs/commerce-operations/implementation-playbook/glossary)이(가) `mounts`에 저장되어 있습니다. 로컬 환경과 같은 소스 마운트 위치에서 대상 마운트 위치로 파일을 마이그레이션하는 방법에는 두 가지가 있습니다. Adobe 두 방법 모두 `rsync` 유틸리티를 사용하지만 로컬 환경과 원격 환경 간에 파일을 이동할 때는 `magento-cloud` CLI를 사용하는 것이 좋습니다. 또한 Adobe은 원격 원본에서 다른 원격 위치로 파일을 이동할 때 `rsync` 메서드를 사용하는 것이 좋습니다.

### CLI를 사용하여 파일 마이그레이션

`mount:upload` 및 `mount:download` CLI 명령을 사용하여 로컬 환경과 원격 환경 간에 파일을 마이그레이션할 수 있습니다. 두 명령 모두 `rsync` 유틸리티를 사용하지만 CLI 명령은 클라우드 인프라 환경의 Adobe Commerce에 맞는 옵션과 프롬프트를 제공합니다. 예를 들어 옵션 없이 단순 명령을 사용하는 경우 CLI는 업로드 또는 다운로드할 마운트 또는 마운트를 선택하라는 메시지를 표시합니다.

```bash
magento-cloud mount:download
```

샘플 응답:

```
Enter a number to choose a mount to download from:
  [0] app/etc
  [1] pub/static
  [2] var
  [3] pub/media
  [4] All mounts
 > 3

Target directory: ~/pub/media/

Downloading files from the remote mount pub/media to pub/media

Are you sure you want to continue? [Y/n] Y
```

**로컬 `pub/media/` 폴더에서 현재 환경의 원격 `pub/media/` 폴더로 파일을 업로드하려면**:

```bash
magento-cloud mount:upload --source /path/to/project/pub/media/ --mount pub/media/
```

샘플 응답:

```
Uploading files from pub/media to the remote mount pub/media

Are you sure you want to continue? [Y/n] Y

  building file list ...   done
  ./
  sample-file.jpeg

  sent 8.43K bytes  received 48 bytes  3.39K bytes/sec
  total size is 154.57K  speedup is 18.23
```

더 많은 옵션을 보려면 `mount:upload` 및 `mount:download` 명령에 `--help` 옵션을 사용하십시오. 예를 들어 마이그레이션하는 동안 불필요한 파일을 제거하는 `--delete` 옵션이 있습니다.

### rsync를 사용하여 파일 마이그레이션

또는 `rsync` 유틸리티를 사용하여 파일을 마이그레이션할 수 있습니다.

```bash
rsync -azvP <source> <destination>
```

이 명령은 다음 옵션을 사용합니다.

- `a` 보관
- 마이그레이션하는 동안 `z`-압축 파일
- `v`-자세한 정보
- `P` 부분 진행률

[rsync](https://linux.die.net/man/1/rsync) 도움말을 참조하십시오.

>[!NOTE]
>
>미디어를 원격 환경에서 원격 환경으로 직접 전송하려면 SSH 에이전트 전달을 사용하도록 설정해야 합니다. [GitHub 지침](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding)을 참조하세요.

**원격 환경에서 원격 환경으로 정적 파일을 직접 마이그레이션하려면(빠른 접근 방식)**:

1. SSH를 사용하여 소스 환경에 로그인합니다. `magento-cloud` CLI를 사용하지 마십시오. `-A` 옵션을 사용하면 인증 에이전트 연결을 전달할 수 있으므로 이 옵션을 사용하는 것이 중요합니다.

   >[!TIP]
   >
   >[!DNL Cloud Console]에서 **SSH 액세스** 링크를 찾으려면 환경을 선택하고 **사이트 액세스**&#x200B;를 클릭합니다.

   ```bash
   ssh -A <environment_ssh_link@ssh.region.magento.cloud>
   ```

1. `rsync` 명령을 사용하여 원본 환경의 `pub/media` 디렉터리를 다른 원격 환경으로 복사합니다.

   ```bash
   rsync -azvP pub/media/ <destination_environment_ssh_link@ssh.region.magento.cloud>:pub/media/
   ```

1. 다른 원격 환경에 로그인하여 파일이 성공적으로 마이그레이션되었는지 확인합니다.

## 데이터베이스 마이그레이션

>[!WARNING]
>
>데이터베이스 가져오기 및 내보내기 작업은 시간이 오래 걸릴 수 있으며, 이는 사이트 성능 및 가용성에 영향을 미칠 수 있습니다. 사용량이 적은 시간 동안 가져오기 및 내보내기 작업을 예약하여 프로덕션 사이트의 성능 저하 또는 중단을 방지합니다.

>[!BEGINSHADEBOX]

**필수 구성 요소:** 데이터베이스 덤프(3단계 참조)에는 데이터베이스 트리거가 포함되어야 합니다. 덤프하려는 경우 [TRIGGER 권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_trigger)이 있는지 확인하십시오.

>[!IMPORTANT]
>
>통합 환경 데이터베이스는 엄격히 개발 테스트를 위한 것이며 스테이징 및 프로덕션으로 마이그레이션하지 않으려는 데이터를 포함할 수 있습니다.

지속적인 통합 배포의 경우 **Adobe은 데이터를 통합에서 스테이징 및 프로덕션으로 마이그레이션하는 것을 권장하지 않습니다**. 테스트 데이터를 전달하거나 중요한 데이터를 덮어쓸 수 있습니다. 빌드 및 배포 중 [구성 파일](../store/store-settings.md) 및 `setup:upgrade` 명령을 사용하여 중요한 구성이 전달됩니다.

>[!ENDSHADEBOX]

**Adobe은 모든 서비스 및 설정을 사용하여 프로덕션 환경에 저장하고 사이트를 완전히 테스트하려면 프로덕션에서 스테이징으로 데이터를 마이그레이션하는 것이 좋습니다**.

>[!NOTE]
>
>원격 환경에서 원격 환경으로 직접 미디어를 전송하려면 ssh 에이전트 전달을 사용하도록 설정해야 합니다. [GitHub 지침](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding)을 참조하세요.

### 데이터베이스 백업

데이터베이스의 백업을 만드는 것이 좋습니다. 다음 절차에서는 [데이터베이스 백업](../storage/database-dump.md)의 지침을 사용합니다.

**데이터베이스를 덤프**:

1. [SSH를 사용하여 복사할 데이터베이스가 포함된 원격 환경에 로그인합니다](../development/secure-connections.md#use-an-ssh-command).

1. 환경 관계를 나열하고 데이터베이스 로그인 정보를 확인합니다.

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"]))->database);'
   ```

   Pro Staging 및 Production의 경우 데이터베이스 이름은 `MAGENTO_CLOUD_RELATIONSHIPS` 변수에 있습니다(일반적으로 응용 프로그램 이름 및 사용자 이름과 동일).

1. 데이터베이스의 백업을 만듭니다. DB 덤프의 대상 디렉터리를 선택하려면 `--dump-directory` 옵션을 사용합니다.

   Starter 환경 및 Pro 통합 환경의 경우 `main`을(를) 데이터베이스 이름으로 사용합니다.

   ```bash
   php vendor/bin/ece-tools db-dump main
   ```

   덤프 옵션:
   - `--dump-directory=<dir>`—데이터베이스 덤프의 대상 디렉터리를 선택하십시오.
   - `--remove-definers`—데이터베이스 덤프에서 DEFINER 문 제거

1. ECE-Tools 메서드가 선호되지만 다른 방법은 GZIP 형식의 기본 MySQL을 사용하여 데이터베이스 덤프 파일을 만드는 것입니다.

   ```bash
   mysqldump -h <database-host> --user=<database-username> --password=<password> --single-transaction --triggers <database-name> | gzip - > /tmp/database.sql.gz
   ```

   대상 환경에 이중 인증을 구성한 경우 데이터베이스 마이그레이션 후 관련 2FA 테이블을 제외하여 다시 구성하지 않는 것이 좋습니다.

   ```bash
   mysqldump -h <database-host> --user=<database-username> --password=<password> --single-transaction --triggers --ignore-table=<database-name>.tfa_user_config --ignore-table=<database-name>.tfa_country_codes <database-name> | gzip - > /tmp/database.sql.gz
   ```

1. SSH 연결을 종료하려면 `logout`을(를) 입력하십시오.

### 데이터베이스 삭제 및 다시 만들기

데이터를 가져올 때 데이터베이스를 삭제하고 만들어야 합니다.

**데이터베이스를 삭제하고 다시 만들려면**:

1. 원격 환경에 대한 [SSH 터널](../development/secure-connections.md#ssh-tunneling)을(를) 설정합니다.

1. 데이터베이스 서비스에 연결합니다.

   ```bash
   mysql --host=127.0.0.1 --user='<database-username>' --pass='<user-password>' --database='<name>' --port='<port>'
   ```

1. `MariaDB [main]>` 프롬프트에서 데이터베이스를 삭제합니다.

   Starter 및 Pro 통합의 경우

   ```shell
   drop database main;
   ```

   프로덕션의 경우:

   ```shell
   drop database <cluster-id>;
   ```

   스테이징의 경우:

   ```shell
   drop database <cluster-ID_stg>;
   ```

1. 데이터베이스를 다시 만듭니다.

   Starter 및 Pro 통합의 경우

   ```shell
   create database main;
   ```

1. 데이터베이스를 가져옵니다.

   프로덕션용 가져오기:

   ```shell
   zcat <cluster-ID>.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h 127.0.0.1 -p -u <database-username> <database-name>;
   ```

   스테이징을 위해 가져오기:

   ```shell
   zcat <cluster-ID_stg>.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h 127.0.0.1 -p -u <database-username> <database-name>;
   ```

   이 명령은 데이터베이스 덤프 파일의 압축을 풀고 `DEFINER` 문을 제거한 다음 지정한 자격 증명을 사용하여 데이터베이스를 가져옵니다.
