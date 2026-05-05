---
source-git-commit: 7abea6614a5c817cef3f83b293fab98974d4b072
workflow-type: tm+mt
source-wordcount: '63'
ht-degree: 0%

---
# 클라우드용 PHP 확장

<table style="table-layout:auto">
    <thead>
      <tr>
        <th>
            기본 확장
        </th>
        <th>
            제거할 수 없는 설치된 확장
        </th>
        <th>
            필요에 따라 설치 및 제거할 수 있는 확장
        </th>
      </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <code>bcmath</code><br>
                <code>bz2</code><br>
                <code>calendar</code><br>
                <code>exif</code><br>
                <code>gd</code><br>
                <code>gettext</code><br>
                <code>intl</code><br>
                <code>libxml</code><br>
                <code>mysqli</code><br>
                <code>pcntl</code><br>
                <code>pdo_mysql</code><br>
                <code>Reflection</code><br>
                <code>soap</code><br>
                <code>sockets</code><br>
                <code>SPL</code><br>
                <code>standard</code><br>
                <code>swoole</code><br>
                <code>sysvmsg</code><br>
                <code>sysvsem</code><br>
                <code>sysvshm</code><br>
                <code>zip</code><br>
                <code>zlib</code><br>
            </td>
            <td>
                <code>ctype</code><br>
                <code>curl</code><br>
                <code>date</code><br>
                <code>dba</code><br>
                <code>dom</code><br>
                <code>fileinfo</code><br>
                <code>filter</code><br>
                <code>ftp</code><br>
                <code>hash</code><br>
                <code>iconv</code><br>
                <code>json</code><br>
                <code>mbstring</code><br>
                <code>mysqlnd</code><br>
                <code>openssl</code><br>
                <code>pcre</code><br>
                <code>pdo</code><br>
                <code>pdo_sqlite</code><br>
                <code>phar</code><br>
                <code>posix</code><br>
                <code>readline</code><br>
                <code>session</code><br>
                <code>sqlite3</code><br>
                <code>tokenizer</code><br>
                <code>xml</code><br>
                <code>xmlreader</code><br>
                <code>xmlwriter</code><br>
            </td>
            <td>
                <code>igbinary</code><br>
                <code>imap</code><br>
                <code>ldap</code><br>
                <code>mcrypt</code><br>
                <code>mysqli</code><br>
                <code>pdo_mysql</code><br>
                <code>propro</code><br>
                <code>recode</code><br>
                <code>redis</code><br>
                <code>shmop sockets</code><br>
                <code>sodium</code><br>
                <code>xmlrpc</code><br>
                <code>xsl</code><br>
            </td>
        </tr>
    </tbody>
</table>

>[!NOTE]
>
>일부 PHP 확장에는 환경별 설치 제한 사항이 있으며 위의 표에 완전히 표시되지 않습니다. 예를 들어 프로젝트 구성을 통해 통합 환경에서 [!DNL LDAP]을(를) 활성화할 수 있지만 `.magento.app.yaml`을(를) 통한 Pro Staging 및 Production에 대한 셀프 서비스 구성이 아닙니다.
