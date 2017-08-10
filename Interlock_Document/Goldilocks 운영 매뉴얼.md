### Goldilocks 운영 매뉴얼


#### 목차

1. 개요
2. Goldilocks 설치
3. Goldilocks 실행 및 종료
4. Goldilocks 접속 및 gSQL 명령어
5. Listener 실행 및 종료
6. 백업
7. 복구
8. 이중화
9. Goldilocks 데이터 적재 및 추출



#### 1. 개요
* Goldilocks 를 운영하면서 발생할 수 있는 작업에 대해 기술한다.
* 이 문서는 'Goldilocks 관리자'를 대상으로 작성되었다.
* 이 문서는 Goldilocks 3.1.0 standalone 버전을 기반으로 작성되었다.





#### 2. Goldilocks 설치
* 'Goldilocks 설치 매뉴얼' 문서를 참고하며, 이 문서에서는 간략하게 작성한다.
* Goldilocks 패키지는 Linux, HP-UX, AIX 운영체제에서 지원된다.

###### 2 - 1. Goldilocks 패키지 다운로드

* Goldilocks 패키지는 technet@sunjesoft.com 메일로 문의한다.

###### 2 - 2. Goldilocks 패키지 설치

###### 2 - 3. 환경변수 등록

###### 2 - 2. Goldilocks 생성

* gcreatedb 명령어를 사용하여 Goldilocks 를 생성한다.






#### 3. Goldilocks 구동 및 종료
* Goldilocks 를 구동 및 종료하기 위해서는 DBA 권한이 필요하다.
* DBA 권한을 가진 계정으로 접속하기 위해서는 접속시 '--as sysdba' 를 작성해 주어야 한다.

###### 3 - 1. Goldilocks 구동

* Goldilocks 는 구동단계에 따라 IDLE - NOMOUNT - MOUNT - OPEN 단계로 구성된다.
* 구동단계는 명시적 혹은 암묵적인 방법으로 나뉜다.


[ 명시적 OPEN 방법 ]


      Shell> gsql SYS <password> --as sysdba
      gSQL> \STARTUP NOMOUNT;
      gSQL> ALTER SYSTEM MOUNT DATABASE;
      gSQL> ALTER SYSTEM OPEN DATABASE;
      gSQL>

[ 암묵적 OPEN 방법 ]


      Shell> gsql SYS <password> --as sysdba
      gSQL> \STARTUP;
      gSQL>

###### 3 - 2. Goldilocks 종료

* Goldilocks 는 4가지 옵션(NORMAL, TRANSACTIONAL, IMMEDIATE, ABORT)에 따라 다른 방법으로 종료된다.
* Goldilocks 는 종료시 IMMEDIATE 옵션 부여를 권고한다.


| 옵션 | 설명 |
|:--   |:--  |
|NORMAL|새로운 세션의 접속을 차단하고 현재 접속된 모든 세션을 기다린 후, 체크포인트를 수행한 뒤 Instance 를 종료한다.     |
|TRANSACTIONAL|새로운 트랜잭션의 시작을 차단하고 현재 수행중인 트랜잭션이 종료되기를 기다린 후, 체크포인트를 수행하고 Instance 를 종료한다.    |
|IMMEDIATE    |새로운 단위 연산의 수행을 차단하고 현재 수행중인 모든 단위연산의 종료를 기다린 후, 모든 트랜잭션을 Rollback 시킨 후, 체크포인트를 수행하고 Instance 를 종료한다.    |
|ABORT        |접속중인 세션의 상태와 무관하게 바로 Instance 를 종료한다.|


[ Goldilocks 종료 ]

    Shell> gsql SYS <password> --as sysdba
    gSQL> \SHUTDOWN IMMEDIATE;
    gSQL>





#### 4. Goldilocks 접속
* Goldilocks 는 접속을 위해 gsql 유틸리티를 제공하며 gsql 은 SQL 혹은 고유 gSQL 명령어를 수행 할수 있는 대화형 실행파일이다.
* Goldilocks 는 관리자 접속과 일반사용자 접속으로 나뉘어 진다.
* 관리자는 --as sysdba 와 --as admin 으로 나뉘어지며, --as admin 은 --as sysdba 로도  접속 할 수 없는 비상시에 사용된다.
* Goldilocks 는 기본적으로 일반사용자 TEST 계정을 제공한다.


###### 4 - 1. 관리자 접속

* 접속 방법은 UserID Password --as sysdba/admin 으로 구성된다.

[ 관리자 접속 ]

    Shell> gsql SYS <password> --as sysdba
    gSQL>

    Shell> gsql SYS <password> --as admin
    gSQL>

###### 4 - 2. 일반 사용자 접속

* 접속 방법은 UserID Password 로 구성된다.

[ 일반 사용자 접속 ]

    Shell> gsql TEST <password>
    gSQL>

###### 4 - 3. gsql 사용법

[ gsql 사용법 ]

    $ gsql --help


    Usage

        gsql [user_name [password]] [options]

    Arguments:

        user_name       user name
        password        password

    Options:

        --version                      print version infomation and exit
        --import       FILE            import sql FILE
        --no-prompt                    suppresses the display of the banner and prompts
        --dsn          DSN             dsn string (default is GOLDILOCKS)
        --conn-string  'CONN-STRING'   connection string
        --prompt       STRING          change prompt string
        --enable-color                 enable colored text
        --as           {SYSDBA|ADMIN}  privilege
        --silent                       suppresses the display of the result message and echoing of commands
        --help                         print help message

###### 4 - 4. gsql 명령어
* Goldilocks 는 SQL 이 아닌 gsql 고유 명령어를 제공한다.
* gsql 명령어를 사용하기 위해서는 앞에 '\' 를 붙여야 한다.

[ gSQL 명령어 ]    

        gSQL> \help
        \help
        \q[uit]
        \i[mport] {'FILE'}               Import SQL
        \ed[it] [{'FILE'|[HISTORY] num}] Edit SQL statement
        \\                               Executes the most recent history entry
        \{n}                             Executes n'th history entry
        \hi[story]                       Show history entries
        \desc     {[schema.]table_name}  Show table description
        \idesc    {[schema.]index_name}  Show index description
        \spo[ol]  ['filename' | OFF]     Stores query results in a file
        \ho[st]   [command]              Executes an operating system command
        \set vertical     {ON|OFF}
        \set time         {ON|OFF}
        \set timing       {ON|OFF}
        \set color        {ON|OFF}
        \set error        {ON|OFF}
        \set autocommit   {ON|OFF}
        \set autotrace    {ON|TRACEONLY|OFF}
        \set serveroutput {ON|OFF}
        \set linesize     {n}      0 < n <= 100000
        \set pagesize     {n}      0 < n <= 100000
        \set colsize      {n}      0 < n <= 104857600
        \set numsize      {n}      0 < n <= 50
        \set ddlsize      {n}      0 < n <= 100000
        \set history      {n}      n <= 100000 ( if n < 0, clear history buffer )
        \var             {host_var_name} {INTEGER|BIGINT|VARCHAR(n)}
        \exec            [{:host_var_name} := {constant}]
        \exec sql        {sql string}
        \prepare sql     {sql string}
        \dynamic sql     {host_var_name}
        \explain plan    [{ON|ONLY}] {sql string}
        \print           [{host_var_name}]
        \ddl_cluster
        \ddl_db
        \ddl_tablespace    {name}
        \ddl_profile       {name}
        \ddl_auth          {name}
        \ddl_schema        {name}
        \ddl_table         {[schema.]name}
        \ddl_constraint    {[schema.]name}
        \ddl_index         {[schema.]name}
        \ddl_view          {[schema.]name}
        \ddl_sequence      {[schema.]name}
        \ddl_synonym       {[schema.]name}
        \ddl_publicsynonym {name}
        \ddl_procedure     {[schema.]name}
        \startup         {[nomount|mount|open]}
        \shutdown        {[abort|immediate|transactional|normal]}
        \cstartup        {[nomount|mount|open]}
        \cshutdown       {[abort|immediate|transactional|normal]}
        \connect         [userid password] [as {sysdba|admin}]





#### 5. Listener 실행 및 종료
* Goldilocks Listener 는 glsnr 로 실행 및 종료된다.
* Goldilocks Listener 구동 시 세션이 TCP 모드로 접속할 수 있다.

###### 5 - 1. Listener 실행

[ Listener 실행 ]

    Shell> glsnr --start
    Listener is started successfully.

###### 5 - 2. Listener 종료

[ Listener 종료 ]

    Shell> glsnr --stop
    Listener is stopped.

###### 5 - 3. Listener 상태 조회


[ Listener 상태 조회 ]

    Shell> glsnr --status
    ...
    TCP Listen Host : 0.0.0.0, Port : 22581

    Listener is running.





#### 6. 백업
* 'Goldilocks 백업 및 복구 매뉴얼' 문서를 참고하며, 이 문서에서는 간략하게 작성한다.
* Goldilocks 는 오프라인백업(콜드백업), 온라인백업(핫백업, 증분백업)을 지원한다.
* 온라인백업을 수행하기 위해서는 Goldilocks 가 아카이브 모드로 운영되어야 한다.

###### 6 - 1. 콜드 백업 ( 오프라인 백업 )
* Goldilocks 를 종료한 후, 파일을 물리적으로 복사한다.

[ 콜드 백업 ]

    Shell> gsql SYS <password> --as sysdba
    gSQL> \SHUTDOWN IMMEDIATE

    Shell> cp $GOLDILOCKS_DATA backup/


###### 6 - 2. 핫 백업 ( 온라인 백업 )
* Goldilocks 를 백업 모드로 변경한 후, 파일을 물리적으로 복사한다.

[ 핫 백업 ]

    Shell> gsql SYS <password> --as sysdba
    gSQL> ALTER DATABASE BEGIN BACKUP;

    Shell> cp $GOLDILOCKS_DATA backup/

    Shell> gsql SYS <password> --as sysdba
    gSQL> ALTER DATABASE END BACKUP;


###### 6 - 3. 증분 백업 ( 온라인 백업 )
* 가장 처음의 증분백업은 LEVEL 0 으로 시작하며, 이 후 변경된 데이터만 하고싶은경우 LEVEL 을 증가하면서 진행한다.
* LEVEL 은 총 0 ~ 4 단계로 구성된다.
* 증분백업 수행시 $GOLDILOCKS_DATA/backup 경로에 control.inc 와 database.inc 파일이 생성된다.

[ 증분 백업 ]

    Shell> gsql SYS <password> --as sysdba
    gSQL> ALTER DATABASE BACKUP INCREMENTAL LEVEL 0;
    gSQL> ALTER DATABASE BACKUP INCREMENATL LEVEL 1;


#### 7. 복구
* 'Goldilocks 백업 및 복구 매뉴얼' 문서를 참고하며, 이 문서에서는 간략하게 작성한다.
* 복구는 재시작 복구와, 미디어 복구로 나뉜다.
* 재시작 복구는 Goldilocks 구동시 일관성 있는 상태로 복구한다.
* 미디어 복구는 백업받은 파일을 이용하여 복구를 수행한다.
* 이 문서에서는, 모든 파일 유실 시 증분 백업을 이용한 복구 절차에 대해 작성한다.

###### 7 - 1. 미디어 복구 ( 모든 파일 유실 시 )
* 장애발생 후, 아카이브 로그파일을 시퀀스에 맞게 리두로그 파일로 복사한다.
* Goldilocks 를 복구한다.

[ 증분백업을 이용한 미디어 복구 ]

    Shell> gsql SYS <password> --as sysdba
    gSQL> \STARTUP NOMOUNT
    gSQL> ALTER DATABASE RESTORE CONTROLFILE FROM '$GOLDILOCKS_DATA/backup/controlD...inc';
    gSQL> ALTER SYSTEM MOUNT DATABASE;
    gSQL> ALTER DATABASE RESTORE;
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;



#### 8. 이중화

* 'Goldilocks 이중화 매뉴얼' 문서를 참고하며, 이 문서에서는 간략하게 설명한다.
* Goldilocks 는 cyclone 이라는 CDC 방식을 사용하여 양 Goldilocks 를 동기화하는 유틸리티를 제공한다.
* cyclone 은 리두로그에 저장된 내용을 분석하여 동기화하기 때문에, 비동기방식만을 지원한다.
* cyclone 은 Group 단위로 수행되며 이는 이중화 노드와 동일하다.

###### 8 - 1. 이중화 제약사항

* Goldilocks 는 아카이브 모드로 운영되어야 한다.
* 이중화 대상 테이블은 반드시 Primary Key 를 가져야 한다.
* Commit 된 트랜잭션만을 이중화 한다.
* Primary Key 의 값이 갱신되는 경우 해당 테이블은 이중화 대상이 되지 않는다.
* 이중화가 수행중인 테이블에 DDL 이 발생하는 경우 이중화 대상이 되지 않는다.
* 이중화 대상이 아닌 테이블들에 대해 reset 옵션을 부여하여 이중화에 참여시킬 수 있다.
* 이중화 대상 테이블은 Master 와 Slave 간 동일한 컬럼구조를 가져야 한다.
* 이중화 대상 테이블은 동일한 인코딩을 가져야 한다.

###### 8 - 2. 노드 추가

* 모든 프로퍼티들에 대해서 작성되지 않았으며, 자세한 내용은 'Goldilocks 이중화 매뉴얼'을 참고한다.
* 추가하고자 하는 Group 을 Master 와 Slave 환경파일에 등록한다.
* 각 Group 은 고유 Port 를 가져야 한다.


[ Master 환경파일 ]


    GROUP_NAME = Group1
    {
      PORT = 21102
      CAPTURE_TABLE =
      (
        MASTER_TABLE
      )
    }

    GROUP_NAME = Group2
    {
      ...
    }

[ Slave 환경파일 ]

    GROUP_NAME = Group1
    {
      PORT = 21102
      APPLY_TABLE =
      (
        TEST_TABLE TO SLAVE_TABLE
        )
    }

    GROUP_NAME = Group2
    {
      ...
    }


###### 8 - 3. 이중화 수행, 종료 및 상태 조회

* 이중화는 Group 별 혹은 File 별로 수행할 수 있다.

[ Master 구동 ]


    Shell> cyclone --master --start --conf master.sample.conf
    Shell> cyclone --master --start --conf master.sample2.conf --group Group1

[ Master 종료 ]


    Shell> cyclone --master --stop --conf master.sample.conf
    Shell> cyclone --master --stop --conf master.sample2.conf --group Group1

[ Slave 구동 ]

    Shell> cyclone --slave --start --conf slave.sample.conf
    Shell> cyclone --slave --start --conf slave.sample2.conf --group Group1

[ Slave 종료 ]

    Shell> cyclone --slave --stop --conf slave.sample.conf
    Shell> cyclone --slave --stop --conf slave.sample2.conf --group Group1

[ Master, Slave 조회 ]

    Shell> cyclone --master --status
    Shell> cyclone --slave --status


#### 9. Goldilocks 데이터 적재 및 추출
* Goldilocks 는 데이터를 적재 및 추출하기 위해 gloader 유틸리티를 지원한다.
* gloader 는 적재 및 추출을 위한 컨트롤 파일과, 데이터파일(적재시) 이 있어야 한다.

###### 9 - 1. 컨트롤 파일
* 컨트롤 파일은 다음과 같은 형식으로 구성된다.

[ 컨트롤 파일 ]

    TABLE <Schema.name>.<Table_name>
    FIELDS TERMINATED BY 'Delimiter'
    OPTIONALLY ENCLOSED BY 'Qualifier'
    RTRIM ON|OFF
    LTRIM ON|OFF

###### 9 - 2. 데이터 파일
* 데이터 파일은 컨트롤 파일의 Delimiter 와 Qualifier 에 의해 동작된다.
* 테이블에 아래의 데이터가 있고, 컨트롤파일의 Delimiter 와 Qualifier 이 각각 ',' '"' 로 구성된 경우 내려받는 데이터파일은 다음과 같은 형식으로 구성되어 진다.

[ 테이블 내용 C1 ( NUMBER ), C2 ( VARCHAR ) ]

    C1   C2
    __ ____
     1 This
     2   is
     3    a
     4 TEST


[ 데이터 파일 ]

    "1", "This"
    "2", "is"
    "3", "a"
    "4", "TEST"

###### 9 - 3. gloader 사용법
* gloader 사용법은 다음과 같다.

[ gloader 옵션 ]

    $ gloader --help

     Copyright © 2010 SUNJESOFT Inc. All rights reserved.
     Release Trunk.3.1.0 revision(22655)

    Usage

        gloader user_name password mode control data [format] [options]

    arguments:

        user_name             user name
        password              password

    mode: gloader's mode.

        -e, --export                            export data
        -i, --import                            import data

    control:

        -c, --control         FILE              control file

    data:

        -d, --data            FILE              data file

    format:

        -f, --format          TEXT or BINARY    file format (Default text)

    options:

        -l, --log             FILE              log file
        -b, --bad             FILE              bad file
        -D, --dsn             DSN               dsn string
        -a, --array           number of array   number of rows in bind array (Default 1000)
        -m, --filesize        byte              max file size
        -C, --commit          commit unit       number of commit unit (Default 5000)
        -o, --comment         comment           commenting on commit
        -A, --atomic                            use atomic function
        -p, --parallel        thread unit       use parallel in import
        -g, --propagation     [on|off]          enabling or disabling a redo log propagation
        -E, --errors          error count       number of error count to allow (Default 100)
        -t, --AsTIMESTAMP                       bind DATE as TIMESTAMP
        -B, --buffered                          buffered disk io (Default direct io)
        -n  --no-copyright                      suppresses the display of the banner
        -s, --silent                            suppresses the display of the result message

    help:

        -h, --help                              print help message


* sample.ctl 의 컨트롤파일과 sample.dat 의 데이터 파일을 사용하여 TEST 계정에 적재 혹은 추출

[ 추출 ]

    Shell> gloader TEST <password> -e -c sample.ctl -d sample.dat


[ 적재 ]

    Shell> gloader TEST <password> -i -c sample.ctl -d sample.dat
