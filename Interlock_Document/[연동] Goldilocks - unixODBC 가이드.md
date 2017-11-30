# Goldilocks - unixODBC 연동 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS ODBC DRIVER 를 이용하여 unixODBC 와 연동하는 방법을 설명한다.

#### 1 - 2. 이 문서는 http://www.unixodbc.org/ 에서 제공하는 unixODBC 를 기준으로 설명한다.

#### 1 - 3. 연동을 위한 환경 구축은 사용자가 해야 할 사항이므로, 선재소프트는 그 부분에 대한 기술지원을 진행하지 않는다.

#### 1 - 4. unixODBC 설치는 사용자 계정에서 진행되며, configure 옵션은 필요에 따라 부여한다. 이 문서에서는 설치 경로만 지정한다.

#### 1 - 5. unixODBC 설치 시 컴파일 수행을 위한 환경을 체크하며, 환경에 맞는 바이너리가 없어 에러가 발생하는 경우 직접 설치해야한다.

###### [ 테스트 환경 ]

###### 1. Goldilocks 는 centos 계정에 설치되어 있다.

###### 2. 사전에 centos 계정에 Goldilocks 환경변수가 등록되어 있다.

###### 3. centos 계정에 unixODBC 를 설치한 뒤, unixODBC 를 통해 Goldilocks 에 접속한다.

<h6>

    OS       : CentOS Linux release 7.2.1511
    OS USER  : centos
    DATABASE : Goldilocks 3.1.0 r23226
    gcc      : gcc(GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)
    unixODBC : unixODBC v2.3.4


</h6>

## 2. unixODBC 다운로드

#### 2 - 1. 다운로드 방법 1

###### 1. http://www.unixodbc.org/ 사이트에 접속한다.

###### 2. Download 탭을 클릭한 뒤, tar.gz 압축파일을 다운로드 한다.

###### 3. 다운로드한 압축파일을 OS 로 전송한다.

![unixodbc_01](https://user-images.githubusercontent.com/9734988/33422347-be724928-d5f8-11e7-842b-c71ca64deba9.jpg)

#### 2 - 2. 다운로드 방법 2

###### 1. 클라이언트 OS 에서 wget 명령어를 사용하여 다운로드 한다.

<h6>

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

    unixODBC OS USER   : centos
    DATABASE HOME PATH : /home/centos/goldilocks_home
    unixODBC HOME PATH : /home/centos/unixODBC
    DATABASE IP        : 192.168.0.50
    LISTENER PORT      : 22581

</h6>

###### [ 32 Bit 설치 시 환경변수 ]

<h6>

    export CFLAGS="-m32 -DBUILD_LEGACY_64_BIT_MODE=1"
    export LDFLAGS=-m32
    export CXXFLAGS=-m32

</h6>

#### 3 - 1. tar.gz 파일을 압축해제한다.

#### 3 - 2. 압축해제한 경로로 들어간다.

#### 3 - 3. unixODBC 를 설치한다.

<h6>

    $ tar xzf unixODBC-2.3.4.tar.gz

    $ cd unixODBC-2.3.4

    $ ./configure --prefix=/home/centos/unixODBC

    $ make

    $ make install

</h6>

## 4. odbcinst 확인

#### 4 - 1. odbcinst 실행파일을 수행하여 unixODBC 정보를 확인한다.

<h6>

    $ ./home/centos/unixODBC/bin/odbcinst -j
    unixODBC 2.3.4
    DRIVERS............: /home/centos/unixODBC/etc/odbcinst.ini
    SYSTEM DATA SOURCES: /home/centos/unixODBC/etc/odbc.ini
    FILE DATA SOURCES..: /home/centos/unixODBC/etc/ODBCDataSources
    USER DATA SOURCES..: /home/centos/.odbc.ini
    SQLULEN Size.......: 8
    SQLLEN Size........: 8
    SQLSETPOSIROW Size.: 8

</h6>

<h6>

    unixODBC 64 bit 인 경우 : SQLLEN 8
    unixODBC 32 bit 인 경우 : SQLLEN 4

</h6>

#### 4 - 4. USER DATA SOURCES 경로의 .odbc.ini 파일에 내용을 등록한다.

<h6>

    $ cat /home/centos/.odbc.ini
    [ODBC Data Sources]
    GoldilocksODBC = Goldilocks ODBC Driver

    [GoldilocksODBC]
    # DRIVER : DATABASE ODBC DRIVER
    #  SQLLEN 이 8 인 경우 libgoldilockscs-ul64.so
    #  SQLLEN 이 4 인 경우 libgoldilockscs-ul32.so
    # HOST   : DATABASE SERVER IP
    # PORT   : DATABASE LISTENER PORT
    DRIVER = /home/centos/goldilocks_home/lib/libgoldilockscs-ul64.so
    HOST   = 192.168.0.50
    PORT   = 22581

</h6>

## 5. unixODBC 로 Goldilocks 접속

 1. unixODBC 접속명령어 isql 과, 등록한 ODBC 명을 사용하여 Goldilocks 에 접속한다.

<h6>

    $ cd /home/centos/unixODBC/bin
    $ ./isql -v GoldilocksODBC TEST test
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
