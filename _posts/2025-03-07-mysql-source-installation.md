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

셸에서 아래 명령어를 입력합니다. groupadd 명령어로 mysql 그룹을 생성한 다음, useradd 명령어로 mysql 시스템 계정을 생성하고 그 계정의 primary group을 앞서 만든 mysql 그룹으로 설정합니다.

```
$> sudo groupadd mysql
$> sudo useradd -r -g mysql -s /bin/false mysql
```

실행 후 ```id mysql```을 입력했을 때의 결과가 아래와 같다면 그룹과 계정이 잘 추가된 것입니다.

![preconfiguration-setup]({{site.url}}/images/2025-03-07-mysql-source-installation/preconfiguration-setup.png)

### (2) Build and Install

```
$> tar zxvf mysql-8.4.4.tar.gz
```

위 명령어를 입력해 압축 파일의 압축을 해제하면 아래처럼 mysql-8.4.4라는 이름의 디렉토리가 생성됩니다.

![decompressed]({{site.url}}/images/2025-03-07-mysql-source-installation/decompressed.png)

mysql-8.4.4 디렉토리로 들어가서 bld 디렉토리를 만들고 다시 bld 디렉토리로 들어간 다음 cmake로 빌드합니다.

![cmake]({{site.url}}/images/2025-03-07-mysql-source-installation/cmake.png)


### (3) Postinstallation


## Reference

- [MySQL 8.4 Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/){:target="_blank"}
