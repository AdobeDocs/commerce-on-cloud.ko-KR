---
source-git-commit: 2207508cf769b712705de5d41e0876ed60e5f614
workflow-type: tm+mt
source-wordcount: '921'
ht-degree: 3%

---
# ece-tools

**버전**: 2002.2.4

이 참조에는 `ece-tools` 명령줄 도구를 통해 사용할 수 있는 34개의 명령이 포함되어 있습니다.
클라우드 인프라의 Adobe Commerce에서 `ece-tools list` 명령을 사용하여 초기 목록이 자동으로 생성됩니다.

## 일반

이 참조는 응용 프로그램 코드베이스에서 생성됩니다. 콘텐츠를 변경하려면 _피드백을 주세요_(오른쪽 상단에서 링크 찾기). 기여도 지침이 필요하면 [코드 기여도](https://developer.adobe.com/commerce/contributor/guides/code-contributions/)를 참조하십시오.

### 글로벌 옵션

#### `--help`, `-h`

해당 명령에 대한 도움말을 표시합니다. 명령을 지정하지 않은 경우 list 명령에 대한 도움말을 표시합니다.

- 기본값: `false`
- 값을 수락하지 않음

#### `--quiet`, `-q`

메시지 출력 안 함

- 기본값: `false`
- 값을 수락하지 않음

#### `--verbose`, `-v|-vv|-vvv`

메시지의 자세한 정도를 증가시킵니다. 일반 출력의 경우 1, 자세한 출력의 경우 2, 디버그의 경우 3

- 기본값: `false`
- 값을 수락하지 않음

#### `--version`, `-V`

이 응용 프로그램 버전 표시

- 기본값: `false`
- 값을 수락하지 않음

#### `--ansi`

ANSI 출력 강제(또는 비활성화 —no-ansi)

- 값을 수락하지 않음

#### `--no-ansi`

&quot;—ansi&quot; 옵션 무시

- 기본값: `false`
- 값을 수락하지 않음

#### `--no-interaction`, `-n`

대화식 질문하지 않음

- 기본값: `false`
- 값을 수락하지 않음


## `_complete`

```bash
ece-tools _complete [-s|--shell SHELL] [-i|--input INPUT] [-c|--current CURRENT] [-a|--api-version API-VERSION] [-S|--symfony SYMFONY]
```

셸 완료 제안을 제공하는 내부 명령

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--shell`, `-s`

껍질 유형(&quot;bash&quot;, &quot;fish&quot;, &quot;zsh&quot;)

- 값 필요

#### `--input`, `-i`

입력 토큰의 배열(예: COMP_WORDS 또는 argv)

- 기본값: `[]`
- 값 필요

#### `--current`, `-c`

커서가 있는 &quot;입력&quot; 배열의 인덱스(예: COMP_CWORD)

- 값 필요

#### `--api-version`, `-a`

완료 스크립트의 API 버전

- 값 필요

#### `--symfony`, `-S`

더 이상 사용되지 않음

- 값 필요


## `build`

```bash
ece-tools build
```

응용 프로그램을 빌드합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `completion`

```bash
ece-tools completion [--debug] [--] [<shell>]
```

셸 완료 스크립트 덤프

```
The completion command dumps the shell completion script required
to use shell autocompletion (currently, bash, fish, zsh completion are supported).

Static installation
-------------------

Dump the script to a global completion file and restart your shell:

    bin/ece-tools completion  | sudo tee /etc/bash_completion.d/ece-tools

Or dump the script to a local file and source it:

    bin/ece-tools completion  > completion.sh

    # source the file whenever you use the project
    source completion.sh

    # or add this line at the end of your "~/.bashrc" file:
    source /path/to/completion.sh

Dynamic installation
--------------------

Add this to the end of your shell configuration file (e.g. "~/.bashrc"):

    eval "$(/var/jenkins/workspace/gendocs-ece-tools-cli/bin/ece-tools completion )"
```

### 인수

#### `shell`

셸 유형(예: &quot;bash&quot;), &quot;$SHELL&quot; 환경 변수 값이 제공되지 않으면 사용됩니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--debug`

완료 디버그 로그 추적

- 기본값: `false`
- 값을 수락하지 않음


## `db-dump`

```bash
ece-tools db-dump [-d|--remove-definers] [-a|--dump-directory DUMP-DIRECTORY] [--] [<databases>...]
```

데이터베이스 백업을 만듭니다.

### 인수

#### `databases`

백업용 데이터베이스. 사용 가능한 값: [기본 견적 판매]. 인수 값을 지정하지 않으면 `MAGENTO_CLOUD_RELATIONSHIP` 환경 변수 또는/및 .magento.env.yaml 구성 파일의 `stage.deploy.DATABASE_CONFIGURATION` 속성에 저장된 자격 증명을 사용하여 데이터베이스 백업이 만들어집니다.

- 기본값: `[]`
- 배열

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--remove-definers`, `-d`

데이터베이스 덤프에서 정의자 제거

- 기본값: `false`
- 값을 수락하지 않음

#### `--dump-directory`, `-a`

덤프 저장에 대체 디렉토리 사용

- 값 필요


## `deploy`

```bash
ece-tools deploy
```

응용 프로그램을 배포합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `help`

```bash
ece-tools help [--format FORMAT] [--raw] [--] [<command_name>]
```

명령에 대한 도움말 표시

```
The help command displays help for a given command:

  bin/ece-tools help list

You can also output the help in other formats by using the --format option:

  bin/ece-tools help --format=xml list

To display the list of available commands, please use the list command.
```

### 인수

#### `command_name`

명령 이름

- 기본값: `help`

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--format`

출력 형식(txt, xml, json 또는 md)

- 기본값: `txt`
- 값 필요

#### `--raw`

원시 명령 도움말을 출력하려면

- 기본값: `false`
- 값을 수락하지 않음


## `list`

```bash
ece-tools list [--raw] [--format FORMAT] [--short] [--] [<namespace>]
```

목록 명령

```
The list command lists all commands:

  bin/ece-tools list

You can also display the commands for a specific namespace:

  bin/ece-tools list test

You can also output the information in other formats by using the --format option:

  bin/ece-tools list --format=xml

It's also possible to get raw list of commands (useful for embedding command runner):

  bin/ece-tools list --raw
```

### 인수

#### `namespace`

네임스페이스 이름

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--raw`

원시 명령 목록을 출력하려면

- 기본값: `false`
- 값을 수락하지 않음

#### `--format`

출력 형식(txt, xml, json 또는 md)

- 기본값: `txt`
- 값 필요

#### `--short`

명령의 인수 설명을 건너뛰려면

- 기본값: `false`
- 값을 수락하지 않음


## `patch`

```bash
ece-tools patch
```

사용자 지정 패치를 적용합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `post-deploy`

```bash
ece-tools post-deploy
```

배포 후 작업을 수행합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `run`

```bash
ece-tools run <scenario>...
```

시나리오를 실행합니다.

### 인수

#### `scenario`

시나리오

- 기본값: `[]`
- 필수

- 배열

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `backup:list`

```bash
ece-tools backup:list
```

백업 파일 목록을 표시합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `backup:restore`

```bash
ece-tools backup:restore [-f|--force] [--file [FILE]]
```

중요한 구성 파일을 복원합니다. backup:list를 실행하여 백업 파일 목록을 표시합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--force`, `-f`

백업을 복원하는 동안 기존 파일 덮어쓰기

- 기본값: `false`
- 값을 수락하지 않음

#### `--file`

특정 파일 복구 경로

- 값을 허용합니다.


## `build:generate`

```bash
ece-tools build:generate
```

빌드 단계에 필요한 모든 파일을 생성합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `build:transfer`

```bash
ece-tools build:transfer
```

생성된 파일을 init 디렉터리로 전송합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cloud:config:create`

```bash
ece-tools cloud:config:create <configuration>
```

지정된 빌드, 배포 및 배포 후 변수 구성을 사용하여 `.magento.env.yaml` 파일을 만듭니다. 기존 `.magento.env.yaml` 파일을 덮어씁니다.

### 인수

#### `configuration`

JSON 형식의 구성

- 필수

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cloud:config:update`

```bash
ece-tools cloud:config:update <configuration>
```

지정된 구성으로 기존 `.magento.env.yaml` 파일을 업데이트합니다. `.magento.env.yaml` 파일이 없는 경우 만듭니다.

### 인수

#### `configuration`

JSON 형식의 구성

- 필수

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cloud:config:validate`

```bash
ece-tools cloud:config:validate
```

`.magento.env.yaml` 구성 파일의 유효성을 검사합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `config:dump`

```bash
ece-tools config:dumpdump
```

정적 콘텐츠 배포에 대한 구성을 덤프합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cron:disable`

```bash
ece-tools cron:disable
```

모든 Magento cron 프로세스를 비활성화하고 실행 중인 모든 프로세스를 종료합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cron:enable`

```bash
ece-tools cron:enable
```

Magento cron 프로세스를 활성화합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cron:kill`

```bash
ece-tools cron:kill
```

모든 Magento cron 프로세스를 종료합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `cron:unlock`

```bash
ece-tools cron:unlock [--job-code [JOB-CODE]]
```

&quot;실행 중&quot; 상태에서 멈춘 크론 작업의 잠금을 해제합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--job-code`

잠금 해제할 크론 작업 코드.

- 기본값: `[]`
- 여러 값을 허용합니다.


## `dev:generate:schema-error`

```bash
ece-tools dev:generate:schema-error
```

schema.error.yaml 파일에서 dist/error-codes.md 파일을 생성합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `dev:git:update-composer`

```bash
ece-tools dev:git:update-composer
```

Git에서 배포할 업데이트 작성기입니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `env:config:show`

```bash
ece-tools env:config:show [<variable>...]
```

인코딩된 클라우드 구성 환경 변수를 표시합니다.

### 인수

#### `variable`

표시할 환경 변수, 가능한 옵션: 서비스, 경로, 변수

- 기본값: `[]`
- 배열

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `error:show`

```bash
ece-tools error:show [-j|--json] [--] [<error-code>]
```

오류 ID별 오류 정보 또는 마지막 배포의 모든 오류에 대한 정보를 표시합니다.

### 인수

#### `error-code`

오류 코드, 전달되지 않으면 마지막 배포에서 발생한 모든 오류에 대한 명령 표시 정보

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.

#### `--json`, `-j`

JSON 형식의 결과 가져오기에 사용됨

- 기본값: `false`
- 값을 수락하지 않음


## `module:refresh`

```bash
ece-tools module:refresh
```

구성을 새로 고쳐 새로 추가된 모듈을 활성화합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `schema:generate`

```bash
ece-tools schema:generate
```

스키마 *.dist 파일을 생성합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `wizard:ideal-state`

```bash
ece-tools wizard:ideal-state
```

이상적인 구성 상태를 확인합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `wizard:master-slave`

```bash
ece-tools wizard:master-slave
```

마스터-슬레이브 구성을 확인합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `wizard:scd-on-build`

```bash
ece-tools wizard:scd-on-build
```

빌드 구성에서 SCD를 확인합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `wizard:scd-on-demand`

```bash
ece-tools wizard:scd-on-demand
```

SCD 온디맨드 구성을 확인합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `wizard:scd-on-deploy`

```bash
ece-tools wizard:scd-on-deploy
```

배포 구성 시 SCD를 확인합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.


## `wizard:split-db-state`

```bash
ece-tools wizard:split-db-state
```

DB를 분할하는 기능과 DB가 이미 분할되었는지 여부를 확인합니다.

### 옵션

전역 옵션에 대해서는 [전역 옵션](#global-options)을 참조하십시오.
