# Goldilocks - unixODBC 연동 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS ODBC DRIVER 를 이용하여 unixODBC 와 연동하는 방법을 설명한다.

#### 1 - 2. http://www.unixodbc.org/ 에서 제공하는 unixODBC 를 기준으로 설명한다.

#### 1 - 3. 연동을 위한 환경 구축은 사용자가 해야한다. 선재소프트는 이 부분에 대해 기술지원을 진행하지 않는다.

#### 1 - 4. 설치 시, 옵션으로 설치 경로만 지정한다. 다른 옵션에 대해서는 사용자가 환경에 맞게 설정한다.

#### 1 - 5. 설치 시, 환경이 맞지않아 에러가 발생하는 경우 사용자가 설치해야한다.

###### [ 테스트 환경 ]

###### 1. centos 계정에 unixODBC 를 설치하며, 계정에 Goldilocks 환경변수가 등록되어 있다.

###### 2. centos 계정은 Goldilocks 서버와 통신 가능해야 한다.

<h6>

    OS       : CentOS Linux release 7.2.1511
    DATABASE : Goldilocks 3.1.0 r23226
    gcc      : gcc(GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)
    unixODBC : unixODBC v2.3.4

</h6>

## 2. unixODBC 다운로드

#### 2 - 1. 다운로드 방법 [1]

###### 1. http://www.unixodbc.org/ 사이트에 접속한다.

###### 2. Download 탭을 클릭한 뒤, tar.gz 압축파일을 다운로드 한다.

###### 3. 다운로드한 압축파일을 전송한다.

![unixodbc_01](https://user-images.githubusercontent.com/9734988/33422347-be724928-d5f8-11e7-842b-c71ca64deba9.jpg)

#### 2 - 2. 다운로드 방법 [2]

###### 1. wget 명령어를 사용하여 다운로드 한다.

<h6>

    $ whoami
    centos

    $ wget http://www.unixodbc.org/unixODBC-2.3.4.tar.gz

    --2017-09-13 06:56:49--  http://www.unixodbc.org/unixODBC-2.3.4.tar.gz
    Resolving www.unixodbc.org (www.unixodbc.org)... 87.106.19.214
    Connecting to www.unixodbc.org (www.unixodbc.org)|87.106.19.214|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1830660 (1.7M) [application/x-gzip]
    Saving to: ‘unixODBC-2.3.4.tar.gz’

    100%[===============================>] 1,830,660   7.25KB/s   in 5m 59s

    2017-09-13 07:02:50 (4.98 KB/s) - ‘unixODBC-2.3.4.tar.gz’ saved [1830660/1830660]

</h6>

## 3. unixODBC 설치

###### [ 설치 정보 ]

<h6>

    DATABASE HOME PATH : /home/centos/goldilocks_home
    unixODBC HOME PATH : /home/centos/unixODBC
    DATABASE IP        : 192.168.0.50
    LISTENER PORT      : 22581

</h6>

#### 3 - 1. unixODBC 설치 시, Bit 및 SQLLEN 에 따라 특정 환경변수 및 드라이버를 사용한다.

###### unixODBC 가 32 비트인 경우, Goldilocks 라이브러리도 32 비트여야 한다.

<h6>

| OS</br>비트 | unixODBC</br>비트 | SQLLEN | 환경변수 | Goldilocks 드라이버 |
|:--      |:--            |:--     |:--       |:--                  |
| 64      | 64            | 8      | export CFLAGS="-DBUILD_LEGACY_64_BIT_MODE=1" | libgoldilockscs-ul64.so: <br/>ELF 64-bit LSB shared object, x86-64 |
| 64      | 64            | 4      | export CFLAGS="-DBUILD_LEGACY_64_BIT_MODE=1" | libgoldilockscs-ul32.so: <br/>ELF 64-bit LSB shared object, x86-64 |
| 64      | 32            | 4      | export CFLAGS="-m32 -DBUILD_LEGACY_64_BIT_MODE=1"<br/>export LDFLAGS=-m32<br/>export CXXFLAGS=-m32 | libgoldilockscs.so: <br/>ELF 32-bit LSB shared object, Intel 80386 |

</h6>

#### 3 - 2. tar.gz 파일을 압축해제한다.

#### 3 - 3. 압축해제한 경로로 들어간다.

#### 3 - 4. --prefix 옵션을 사용하여 설치 경로를 지정한다.

<h6>

    $ tar xzf unixODBC-2.3.4.tar.gz

    $ cd unixODBC-2.3.4

    $ ./configure --prefix=/home/centos/unixODBC

    $ make

    $ make install

</h6>

#### 3 - 5. unixODBC 환경변수를 적용한다.

<h6>

    $ export unixODBC=/home/centos/unixODBC

    $ export PATH=$unixODBC/bin:$PATH

    $ export LD_LIBRARY_PATH=$unixODBC/lib:$LD_LIBRARY_PATH

</h6>

## 4. odbcinst 확인

#### 4 - 1. odbcinst 실행파일을 수행하여 unixODBC 정보를 확인한다.

<h6>

    $ odbcinst -j
    unixODBC 2.3.4
    DRIVERS............: /home/centos/unixODBC/etc/odbcinst.ini
    SYSTEM DATA SOURCES: /home/centos/unixODBC/etc/odbc.ini
    FILE DATA SOURCES..: /home/centos/unixODBC/etc/ODBCDataSources
    USER DATA SOURCES..: /home/centos/.odbc.ini
    SQLULEN Size.......: 8
    SQLLEN Size........: 8
    SQLSETPOSIROW Size.: 8

</h6>

#### 4 - 4. USER DATA SOURCES 경로의 .odbc.ini 파일에 Goldilocks 를 등록한다.

<h6>

    $ cat /home/centos/.odbc.ini
    [ODBC Data Sources]
    GoldilocksODBC = Goldilocks ODBC Driver

    [GoldilocksODBC]
    # DRIVER : unixODBC 환경에 맞는 3 - 1 의 Goldilocks Driver 를 참조한다.
    # HOST   : 데이터베이스 서버 IP
    # PORT   : 데이터베이스 리스너 PORT
    DRIVER = /home/centos/goldilocks_home/lib/libgoldilockscs-ul64.so
    HOST   = 192.168.0.50
    PORT   = 22581

</h6>

## 5. unixODBC 접속 바이너리(isql)을 이용하여 Goldilocks 접속테스트

#### 5 - 1. 등록한 ODBC 명을 사용하여 Goldilocks 에 접속한다.

<h6>

    $ isql -v GoldilocksODBC TEST test
     +---------------------------------------+
     | Connected!                            |
     |                                       |
     | sql-statement                         |
     | help [tablename]                      |
     | quit                                  |
     |                                       |
     +---------------------------------------+
     SQL>

</h6>

###### [ 연동이 되지 않는 경우 ]

<h6>

    데이터베이스 서버가 구동중인지 확인한다.
    리스너가 구동중인지 확인한다.
    unixODBC 설치계정에서 Goldilocks 환경변수가 등록되어 있는지 확인한다. (참조 1)

</h6>

<h6>

    (참조 1)
    $ echo $SHELL
    /bin/bash

    $ cat ~/.bash_profile
    # Goldilocks 환경변수
    export GOLDILOCKS_HOME=/home/centos/goldilocks_home
    export PATH=$GOLDILOCKS_HOME/bin:$PATH
    export LD_LIBRARY_PATH=$GOLDILOCKS_HOME/lib:$LD_LIBRARY_PATH

</h6>
