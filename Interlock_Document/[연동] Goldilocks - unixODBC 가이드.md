# Goldilocks - unixODBC 연동 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS ODBC DRIVER 를 이용하여 unixODBC 와 연동하는 방법을 설명한다.

#### 1 - 2. 이 문서는 http://www.unixodbc.org/ 에서 제공하는 unixODBC 를 기준으로 설명한다.

#### 1 - 3. 연동을 위한 환경 구축은 사용자가 해야 할 사항이므로, 선재소프트는 그 부분에 대한 기술지원을 진행하지 않는다.

#### 1 - 4. unixODBC 설치 시 configure 옵션은 필요에 따라 부여하며 이 문서에서는 설치 경로만 지정한다.

#### 1 - 5. unixODBC 설치 시 컴파일 수행을 위한 환경을 체크하며, 환경에 맞는 바이너리가 없어 에러가 발생하는 경우 직접 설치해야한다.

###### [ 테스트 환경 ]

<h6>

    OS       : CentOS Linux release 7.2.1511
    DATABASE : Goldilocks 3.1.0 r23226
    gcc      : gcc(GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)
    unixODBC : unixODBC v2.3.4

</h6>

## 2. unixODBC 다운로드

#### 2 - 1. 다운로드 방법 1

###### 1. http://www.unixodbc.org/ 사이트에 접속한다.

###### 2. Download 탭을 클릭한 뒤, tar.gz 압축파일을 다운로드 한다.

###### 3. 다운로드한 압축파일을 OS 로 전송한다.

<img src="E:\pic\unixODBC\unixODBC_01.jpg" alt="unixODBC_01" style="width : 450px;"/>

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

## 3. unixODBC 설치 환경

###### [ 설치 정보 ]

<h6>

    USER           : centos
    PATH           : /home/centos/unixODBC
    DATABASE IP    : 192.168.0.50
    LISTENER PORT  : 22581

</h6>

[ unixODBC 32 비트 설치 시 환경변수 ]

    export CFLAGS="-m32 -DBUILD_LEGACY_64_BIT_MODE=1"
    export LDFLAGS=-m32
    export CXXFLAGS=-m32

#### 5. unixODBC DRIVER MANAGER 설치

1. tar.gz 파일을 압축해제한다.
2. 압축해제한 경로로 들어간다.
3. unixODBC 를 설치한다.


    $ tar xzf unixODBC-2.3.4.tar.gz

    $ cd unixODBC-2.3.4

    $ ./configure --prefix=/home/centos/unixODBC

    $ make

    $ make install

#### 6. odbcinst 확인

1. unixODBC 가 설치된 경로로 이동한다.
2. bin 폴더로 이동한다.
3. odbcinst 실행파일을 수행하여 unixODBC 정보를 확인한다.


    $ cd /home/centos/unixODBC

    $ cd bin

    $ ./odbcinst -j
    unixODBC 2.3.4
    DRIVERS............: /home/centos/unixODBC/etc/odbcinst.ini
    SYSTEM DATA SOURCES: /home/centos/unixODBC/etc/odbc.ini
    FILE DATA SOURCES..: /home/centos/unixODBC/etc/ODBCDataSources
    USER DATA SOURCES..: /home/centos/.odbc.ini
    SQLULEN Size.......: 8
    SQLLEN Size........: 8
    SQLSETPOSIROW Size.: 8

> 설치가 64 bit 인 경우 SQLLEN 은 8, 32 bit 인 경우 SQLLEN 은 4 이다.

#### 7. odbc.ini 설정

1. USER DATA SOURCES 경로에 Goldilocks ODBC 를 등록한다.


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

#### 8. unixODBC DRIVER MANAGER 연동

 1. 등록한 ODBC 명을 사용하여 Goldilocks 에 접속한다.


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
