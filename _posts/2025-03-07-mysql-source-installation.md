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

Version은 8.4.4 LTS, Operating System은 Source Code, OS Version은 All Operating Systems (Generic) (Architecture Independent)을 선택한 후, 아래 Download 버튼을 클릭해 압축 파일을 다운로드합니다.

![mysql-8.4.4-tar-archive]({{site.url}}/images/2025-03-07-mysql-source-installation/mysql-8.4.4-tar-archive.png)

## 2. Prerequisites

MySQL을 소스 코드로 설치하기 위해서는 아래 목록에 있는 툴들이 필요합니다.

- CMake
- make
- C++ compiler
- OpenSSL library
- ncurses library

시스템 환경에 맞는 패키지 매니저를 이용해 설치하면 됩니다. 우분투를 사용하고 계시다면 아래 명령어로 설치할 수 있습니다.

```
$ sudo apt update

$ sudo apt install cmake
$ sudo apt install make
$ sudo apt install g++
$ sudo apt install libssl-dev
$ sudo apt install libncurses-dev
```

## 3. 설치

소스 코드를 다운로드하고 prerequisites 설치까지 완료되었다면 본격적으로 설치를 진행할 수 있습니다. 아래 MySQL에 나와있는 대로 진행하면됩니다.

![intallation-sequence]({{site.url}}/images/2025-03-07-mysql-source-installation/intallation-sequence.png)

하나하나 해보겠습니다.

### (1) Preconfiguration setup

셸에서 아래 명령어를 입력합니다. groupadd는 mysql 그룹을 만드는 역할을 하고, useradd는 mysql 시스템 계정을 생성하고 mysql 계정의 primary group을 앞서 만든 mysql 그룹으로 설정하는 역할을 합니다.

```
$> sudo groupadd mysql
$> sudo useradd -r -g mysql -s /bin/false mysql
```

실행 후 ```id mysql```을 입력했을 때 아래와 같은 결과가 나타나면 그룹과 계정이 잘 추가된 것입니다.

![preconfiguration-setup]({{site.url}}/images/2025-03-07-mysql-source-installation/preconfiguration-setup.png)

### (2) Build and install

### (3) Postinstallation setup




## Reference





