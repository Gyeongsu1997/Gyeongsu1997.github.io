---
layout: single
title: "MySQL 소스 코드로 설치하기"
category: MySQL
tags:
  - MySQL
toc: true
---

본 문서는 MySQL 서버를 소스 코드로 설치하는 과정을 다루고 있습니다. 운영체제는 Ubuntu 24.04 LTS, MySQL 서버는 MySQL Community Server 8.4.4 LTS를 사용합니다.

## 1. Download

[MySQL Community Downloads 페이지](https://dev.mysql.com/downloads/){:target="_blank"}에서 MySQL Community Server를 클릭합니다.

![mysql-community-downloads]({{site.url}}/images/2025-03-07-mysql-source-installation/mysql-community-downloads.png)

Version은 8.4.4 LTS, Operating System은 Source Code, OS Version은 All Operating Systems (Generic) (Architecture Independent)를 선택한 후, 아래 Download 버튼을 클릭해 압축 파일을 다운로드합니다.

![mysql-8.4.4-tar-archive]({{site.url}}/images/2025-03-07-mysql-source-installation/mysql-8.4.4-tar-archive.png)

## 2. Prerequisites

MySQL을 소스 코드로 설치하기 위해서는 아래 목록에 있는 툴들이 필요합니다.

- make
- CMake
- C++ compiler
- pkg-config
- OpenSSL library
- ncurses library
- RPC library

각자 시스템 환경에 맞는 패키지 매니저를 이용해 설치하시면 됩니다. 만약 우분투를 사용하고 계시다면 아래 명령어로 설치할 수 있습니다.

```
$> sudo apt update
$> sudo apt install make
$> sudo apt install cmake
$> sudo apt install g++
$> sudo apt install pkg-config
$> sudo apt install libssl-dev
$> sudo apt install libncurses-dev
$> sudo apt install libtirpc-dev
```

운영체제나 MySQL 서버의 버전에 따라 추가적으로 필요한 패키지가 있을 수도 있으므로 이후 설치 단계에서 발생하는 오류 메시지를 보고 필요한 패키지를 설치하면 됩니다.

## 3. Installation

소스 코드를 다운로드하고 prerequisites 설치까지 완료되었다면 본격적으로 설치를 진행할 수 있습니다. 기본적으로는 [MySQL 매뉴얼](https://dev.mysql.com/doc/refman/8.4/en/installing-source-distribution.html){:target="_blank"}에 나와있는 것처럼 아래 명령어를 순서대로 입력하면 됩니다.

![intallation-sequence]({{site.url}}/images/2025-03-07-mysql-source-installation/intallation-sequence.png)

하나씩 해보겠습니다.

### (1) Preconfiguration

**groupadd** 명령어로 mysql 그룹을 생성한 다음, **useradd** 명령어로 mysql 시스템 계정을 생성하고 그 계정의 primary group을 앞서 만든 mysql 그룹으로 설정합니다. 실행 후 **id mysql**을 입력했을 때의 결과가 아래와 같다면 그룹과 계정이 잘 추가된 것입니다.

![preconfiguration]({{site.url}}/images/2025-03-07-mysql-source-installation/preconfiguration.png)

### (2) Build and Install

앞서 다운로드한 압축 파일이 위치한 경로에서 **tar** 명령어를 이용해 압축을 해제합니다.

![tar]({{site.url}}/images/2025-03-07-mysql-source-installation/tar.png)

압축을 해제하고 나면 ```mysql-VERSION```이라는 이름의 디렉토리가 만들어집니다. 생성된 디렉토리로 들어가서 bld 디렉토리를 만들고 다시 bld 디렉토리로 들어갑니다.

![decompressed]({{site.url}}/images/2025-03-07-mysql-source-installation/decompressed.png)

bld 디렉토리에서 **cmake ..**를 입력해 빌드 파일을 생성합니다.

![cmake]({{site.url}}/images/2025-03-07-mysql-source-installation/cmake.png)

빌드 파일 생성이 완료되면 **make** 명령어로 빌드를 시작합니다. 빌드 과정이 오래 걸리니 한숨 자고 오시면 됩니다.

![make]({{site.url}}/images/2025-03-07-mysql-source-installation/make.png)

빌드가 완료되면 **sudo make install** 명령어를 입력해 빌드된 실행 파일을 설치합니다. 기본 설치 경로는 ```/usr/local/mysql```입니다. 설치 과정에서 필요한 디렉토리를 생성하려면 루트 권한이 필요하므로 앞에 sudo를 붙여 실행합니다.

![make-install]({{site.url}}/images/2025-03-07-mysql-source-installation/make-install.png)

### (3) Postinstallation

설치가 끝나면 ```/usr/local/mysql``` 디렉토리로 이동해 mysql-files 디렉토리를 생성하고 소유자와 권한을 설정합니다.

![mysql-files]({{site.url}}/images/2025-03-07-mysql-source-installation/mysql-files.png)

```/usr/local/mysql``` 하위의 bin 디렉토리에는 MySQL 서버와 클라이언트 등의 실행 파일이 위치합니다. bin 디렉토리로 이동해 MySQL 서버를 초기화하기 위해 **sudo ./mysqld --initialize --user=mysql**을 입력하면 root@localhost 계정의 임시 비밀번호가 생성됩니다. 최초로 로그인하기 위해 이 비밀번호가 필요하므로 기억해두어야 합니다.

![initialize]({{site.url}}/images/2025-03-07-mysql-source-installation/initialize.png)

## 4. Starting Server

초기화까지 완료되었다면 **sudo ./mysqld_safe --user=mysql &**를 입력해 MySQL 서버를 시작할 수 있습니다.

![start-server]({{site.url}}/images/2025-03-07-mysql-source-installation/start-server.png)

MySQL 서버에 접속하려면 **./mysql -u root -p**를 입력하고 앞서 발급받은 임시 비밀번호를 입력해 로그인합니다.

![mysql-client]({{site.url}}/images/2025-03-07-mysql-source-installation/mysql-client.png)

마지막으로 할 일이 있습니다. 초기화 과정에서 발급받은 임시 비밀번호 대신 사용할 새 비밀번호를 설정하는 것입니다. 이를 위해 아래 SQL문을 실행합니다.

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root-password';
```

![alter-password]({{site.url}}/images/2025-03-07-mysql-source-installation/alter-password.png)

앞으로는 위에서 설정한 새 비밀번호를 사용해 root@localhost 계정으로 로그인할 수 있습니다. 이상으로 MySQL 서버를 소스 코드로 설치하는 과정을 알아보았습니다.

## Reference

- [MySQL 8.4 Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/){:target="_blank"}
