### Goldilocks unixODBC 연동 가이드

#### 1. 개요

1 - 1. GOLDILOCKS ODBC DRIVER 를 이용하여 unixODBC DRIVER MANAGER 와 연동하는 방법을 설명한다.<br/>
1 - 2. 이 문서는 http:www.unixodbc.org/ 에서 제공하는 unixODBC DRIVER MANAGER 를 기준으로 설명한다.<br/>
1 - 3. 연동을 위한 환경 구축은 사용자가 해야 할 사항이므로, 선재소프트는 그 부분에 대한 기술지원을 진행하지 않는다.


이 문서의 테스트 환경은 다음과 같다.

    OS       : LINUX 3.10.0-327.el7.x86_64
    DATABASE : GOLDILOCKS 3.1.0 r22655
    unixODBC : 2.3.4


#### 2. unixODBC DRIVER MANAGER 다운로드

2 - 1.  http://www.unix.odbc.org 사이트에 접속한다.<br/>
2 - 2. 화면 좌측의 Download 를 클릭한다.<br/>
2 - 3. Download 버튼 클릭을 통해 소스를 다운받는다.<br/>


사이트의 UI 는 언제든지 변경 될 수 있다.<br/>
다운로드 받은 제품은 다음과 같은 형태의 압축파일이다.<br/>

    Shell> ls
    unixODBC-2.3.4.tar.gz


#### 3. unixODBC DRIVER MANAGER 설치

3 - 1. 소스를 압축 해제한다. 이 문서에서는 /home/user/unixODBC 경로에 설치한다.


    Shell> cd /home/user/unixODBC
    Shell> tar xvzf unixODBC-2.3.4.tar.gz

3 - 2. 컴파일용 환경변수 설정한다.
3 - 2 - 1. 사용할 ODBC DRIVER 의 bit와 맞는 Goldilocks Library 를 확인한다.

 * 32bit 컴파일은 libgoldilockscs.so 를 64bit 컴파일은 libgoldilockscs-ul32/64.so 를 사용한다.
 * ul32/ul64 는 ODBC DRIVER 의 bit 를 의미하지 않으며 SQLLEN 의 값을 의미한다.
 * ul32 는 SQLLEN=4byte(32bit), ul64 는 SQLLEN=8byte(64bit) 를 의미한다.


     libgoldilockscs-ul32.so
     libgoldilockscs-ul64.so
     libgoldilockscs.so


3 - 2 - 2. SQLLEN 및 SQLULEN 크기 설정

 * SQLLEN 및 SQLULEN 크기는 기본적으로 OS 의 bit 에 맞게 설정된다.
 * SIZEOF_LONG_INT 크기에 따라 32/64bit 가 결정된다.

OS 64 bit 에서 SQLLEN / SQLULEN 을 32bit 로 사용하기 위해서는, 컴파일 환경변수를 다음과 같이 설정한다.


    export CFLAGS=-DBUILD_LEGACY_64_BIT_MODE=1

3 - 3 - 3. unixODBC 컴파일 bit 선택

 * 컴파일 환경변수를 통해서 bit 를 선택할 수 있다.
 * 기본적으로 컴파일은 OS bit 를 따라간다.
 * OS 64bit 에서 32bit 로 컴파일 하려 하는 경우 아래와 같이 설정한다.

Linux 64bit 에서 unixODBC를 32bit 컴파일 설정을 하기 위한 환경변수 예는 다음과 같다.

    export CFLAGS="-m32 -DBUILD_LEGACY_64_BIT_MODE=1"
    export LDFLAGS=-m32
    export CXXFLAGS=-m32


#### 4. make 를 위한 configuration 과정

4 - 1. configuration 시 설치할 디렉토리 (/home/user/unixODBC) 를 설정한다.

* configuration 에서 제공하는 옵션들은, 필요에 따라 부여하며 이 문서에서는 작성하지 않는다.
* configuration 단계에서 컴파일을 수행하기 위한 환경을 체크하는데, 이 때 환경에 맞는 바이너리가 없어 에러가 발생하는 경우 직접 설치해야 한다.


    Shell> ./configure --prefix=/home/user/unixODBC
     * --prefix         : 설치할 경로를 설정 (절대경로 작성)
       기본값은 /usr/local 이며 install 시 prefix 에 설정된 디렉토리에 설치되기 때문에,
       사전에 해당 디렉토리에 대한 접근 및 쓰기 권한이 있어야한다.


4 - 2. make 수행

* configure 가 끝나면 make 를 수행한다.
* make install 이 끝나면 --prefix 에 설정한 경로에 설치된다.


    Shell> make
    Shell> make install



4 - 3. 설치 확인

* install 후 정상적으로 library 를 로딩하는지 확인한다.


    Shell> cd /home/user/unixODBC/bin
    Shell> ./dltest $GOLDILOCKS_HOME/lib/libgoldilockscs-ul32.so
    SUCCESS: Loaded $GOLDILOCKS_HOME/lib/libgoldilockscs-ul32.so

    Shell> ./dltest $GOLDILOCKS_HOME/lib/libgoldilockscs-ul64.so
    SUCCESS: Loaded $GOLDILOCKS_HOME/lib/libgoldilockscs-ul64.so

#### 5. unixODBC DRIVER MANAGER 연동

5 - 1. ini 환경파일경로 및 SQLLEN 값을 확인한다.

    Shell> odbcinst -j
    unixODBC 2.3.4
    DRIVERS............: /home/user/unixODBC/odbcinst.ini
    SYSTEM DATA SOURCES: /home/user/unixODBC/odbc.ini
    FILE DATA SOURCES..: /home/user/unixODBC/ODBCDataSources
    USER DATA SOURCES..: /home/user/.odbc.ini
    SQLULEN Size.......: 8 (32bit 인 경우 4)
    SQLLEN Size........: 8 (32bit 인 경우 4)
    SQLSETPOSIROW Size.: 8

5 - 2. USER DATA SOURCES .odbc.ini 에 다음과 같이 기록한 후 저장한다.

* .odbc.ini 설정파일 예제


    [ODBC Data Sources]
    GoldilocksODBC = Goldilocks ODBC Driver

    [GoldilocksODBC]
    Driver = /home/user/goldilocks_home/lib/libgoldilockscs-ul64.so
    Description = Goldilocks ODBC Driver
    Host = 127.0.0.1
    Port = 22581


* 주요 설정 값

|설정항목 |설정값                |설명                                       |
|:--     |:--                   |:--                                       |
|Driver  |libgoldilocks-ul32.so |컴파일 bit / SQLLEN bit 에 따라 Driver 를 설정한다.   |
|        |libgoldilocks-ul64.so |                                          |
|        |libgoldilockscs.so    |                                          |
|Host    |Server IP             |데이터베이스 서버 IP 를 설정한다.           |
|Port    |Listener PORT         |데이터베이스 서버 glsnr Port 를 설정한다.   |


#### 6. unixODBC 연결 테스트

6 - 1. 설정이 완료된 후, unixODBC 가 설치된 경로에서 isql 툴을 이용하여 접속한다.


    Shell> cd /home/user/unixODBC/bin
    Shell> ./isql -v GoldilocksODBC SYS gliese
    +---------------------------------------+
    | Connected!                            |
    |                                       |
    | sql-statement                         |
    | help [tablename]                      |
    | quit                                  |
    |                                       |
    +---------------------------------------+
    SQL>



#### 7. unixODBC Trace Log 설정

7 - 1. DRIVERS odbcinst.ini 에 다음과 같이 기록한 후 저장한다.

* DSN 명은 반드시 [ODBC] 로 사용해야 한다.
* Trace log 를 남길 위치와, log 기록 수행여부를 기록한다.

* odbcinst.ini 설정파일 예제


    [ODBC]
    TraceFile = /home/user/unixODBC/trace.log
    Trace = Yes
