# Goldilocks Server 설치 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS 를 LINUX, AIX, HP-UX 환경에서 설치하는 방법을 설명한다.

#### 1 - 2. 연동을 위한 환경 구축은 사용자가 해야 할 사항이며, 선재소프트는 이 부분에 대한 기술지원을 진행하지 않는다.

#### 1 - 3. GOLDILOCKS 를 구동하기 위한 운영체제 별 최소 커널 값은 다음과 같다.

<h6>

| 운영체제 | 커널   |
|:--       |:--     |
| LINUX    | 2.6.32 |
| AIX      | 6.1    |
| HP-UX    | 11.31  |

</h6>

## 2. 커널 값 설정

#### 2 - 1. GOLDILOCKS 와 관련하여 지정하는 커널 값에 대한 설명이다.

###### 공유메모리

<h6>

| 커널 변수 | 설명 | 권장 값 |
|:--        |:--   |:--      |
|shmmax     | 하나의 고유메모리 세그먼트가 가지는 최대 크기 | 가장 큰 데이터파일의 크기보다 큰 값 |
|shmmni     | 시스템이 사용가능한 공유메모리 세그먼트의 최대 개수 | 전체 데이터파일 수 + 1 보다 큰 값 |
|shmall     | 전체 공유메모리 세그먼트의 총합 (Page 수) | 테이블스페이스 구성 총합보다 큰 값 (shmmax / shmmni) |

</h6>

###### 세마포어

<h6>

| 커널 변수 | 설명 | 권장 값 |
|:--        |:--   |:--      |
|semmsi     | 세마포어 셋 한 개에 설정되는 세마포어 수 | 250 |
|semmni     | 세마포어 셋의 수 | 128 |
|semmns     | 세마포어 셋의 총 개수 | 32000 |
|semopm     | 시스템 콜 별로 수행할 수 있는 세마포어 수 | 100 |

</h6>

#### 2 - 2. 커널 값을 지정 및 적용한다.

###### 다음은 memory 전체 사용량이 4GB(4294967296 Byte)인 경우 커널 값을 지정하는 방법이다.

###### [LINUX]

###### 커널이 3.0.0 이상인 경우 RemoveIPC 값을 no 로 변경한다.

<h6>

    # vi /etc/sysctl.conf
    ...
    kernel.shmmax = 4294967296
    kernel.shmmni = 4096
    kernel.shmall = 1048576
    kernel.sem = 250 32000 100 128

    # sysctl -p

</h6>

<h6>

    # vi /etc/systemd/logind.conf
    ...
    RemoveIPC=no

</h6>

###### [AIX]

<h6>

    StackOverFlow 요청한상태
    https://stackoverflow.com/questions/47645225/kernel-parameters-setting-on-aix-hpux-linux

</h6>

###### [HP-UX]

<h6>

    StackOverFlow 요청한상태
    https://stackoverflow.com/questions/47645225/kernel-parameters-setting-on-aix-hpux-linux

    # kctune shmmax = 4294967296
    # kctune shmmni = 4096

    # kctune semmsl = 250
    # kctune semmni = 128
    # kctune semmns = 32000

    파라미터를 못찾음
    # kctune shmall = 1048576
    # kctune sem = 250 32000 100 128
    # semopm = 100

</h6>

## 3. Goldilocks 사용자 계정 생성

#### 3 - 1. 사용자 계정을 생성한다.

###### 사용자 계정 환경설정

<h6>

    USER  : sundb
    HOME  : /home/sundb
    SHELL : bash

</h6>

<h6>

    # useradd -d /home/sundb -s /bin/bash sundb

</h6>

#### 3 - 2. 사용자 계정 비밀번호를 변경한다.

<h6>

    # passwd sundb

</h6>

#### 3 - 3. 사용자 계정으로 로그인한다.

<h6>

    # su - sundb

</h6>

## 4. 데이터베이스 설치

###### goldilocks server package 가 없는 경우 technet@sunjesoft.com 에 요청한다.

#### 4 - 1. goldilocks-server-<<version>>-<<os>>-<<bit>>.tar.gz 파일을 압축 해제한다.

<h6>

    $ tar xzf goldilocks-server-*.tar.gz

</h6>

#### 4 - 2. 압축 파일을 풀면 패키지명 폴더 아래 goldilocks_home, goldilocks_data 가 생성된다.

###### [goldilocks_home]

<h6>

| 폴더 | 역할 |
|:--   |:--   |
| bin | 바이너리 폴더 |
| lib | 라이브러리 폴더 |
| include | 헤더파일 폴더 |
| sample | Goldilocks 에 적용 가능한 예제 폴더 (ODBC, JDBC, Embedded-SQL, SQL 포함) |
| admin | 데이터베이스 딕셔너리 폴더 |
| license | 라이센스 폴더 |
| msg | 에러메세지 폴더 |

</h6>

###### [goldilocks_data]

<h6>

| 폴더 | 역할 |
|:--   |:--   |
| archive_log | 아카이빙 로그 폴더 |
| backup      | 백업 폴더 |
| conf        | 프로퍼티 설정 폴더 |
| db          | 데이터 파일 폴더 |
| wal         | 컨트롤 파일, 리두로그 파일 폴더 |
| trc         | 시스템로그 파일 폴더 |

</h6>

#### 4 - 3. 홈 경로에 심볼릭링크를 생성한다.

<h6>

    $ ln -s goldilocks-server-venus.3.1.1-linux-x86_64/goldilocks_home/ /home/sundb/goldilocks_home
    $ ln -s goldilocks-server-venus.3.1.1-linux-x86_64/goldilocks_data/ /home/sundb/goldilocks_data

</h6>

#### 4 - 4. 라이센스를 등록한다.

###### license 가 없는 경우 technet@sunjesoft.com 에 요청한다.

###### goldilocks_home/license 폴더에 license 파일로 저장한다.

<h6>

    $ cp license /home/sundb/goldilocks_home/license/license

</h6>

## 5. 사용자 환경설정

#### 5 - 1. 사용자 환경파일에 GOLDILOCKS 환경변수를 등록 및 적용한다.

###### 환경파일은 SHELL 에 따라 달라질 수 있다.

<h6>

    $ vi ~/.bash_profile
    export GOLDILOCKS_HOME=$HOME/goldilocks_home
    export GOLDILOCKS_DATA=$HOME/goldilocks_data
    export PATH=$GOLDILOCKS_HOME/bin:$PATH
    export LD_LIBRARY_PATH=$GOLDILOCKS_HOME/lib:$LD_LIBRARY_PATH

    $ . ~/.bash_profile

    $ echo $GOLDILOCKS_HOME
    /home/sundb/goldilocks_home
    $ echo $GOLDILOCKS_DATA
    /home/sundb/goldilocks_data

    $ vi ~/.odbc.ini
    [GOLDILOCKS]
    HOST=127.0.0.1
    PORT=22581

</h6>

## 6. 데이터베이스 생성

#### 6 - 1. os 인코딩을 확인한다.

<h6>

    $ echo $LANG

</h6>

#### 6 - 2. 인코딩에 따라 데이터베이스 생성시 옵션을 부여한다.

<h6>

| 인코딩 | 옵션 |
|:--     |:--   |
| utf8   | --character_set UTF8 |
| euckr  | --character_set UHC |

</h6>

#### 6 - 3. 데이터베이스 생성시 옵션을 부여하여 설정을 변경할 수 있다.

<h6>

| 옵션 | 설명 | 기본값 |
|:--   |:--   |:--     |
| db_name | 데이터베이스 명 | goldilocks |
| db_comment | 데이터베이스에 대한 설명 | goldilocks database |
| timezone | 타임존 | +09:00 |
| character_set | 데이터베이스 인코딩 | UTF8 |
| char_length_unit | 문자 단위 | OCTETS |
| home | goldilocks.properties.conf 파일 | $GOLDILOCKS_DATA/conf/goldilocks.properties.conf |
| cluster | 데이터베이스를 클러스터 모드로 변경 | <br/> 옵션 미 부여 시, STAND ALONE 으로 생성|
| member | 클러스터 모드 시 멤버 명 | G1N1 |
| port | 클러스터 모드 시 포트 | 10101 |
| host | 클러스터 모드 시 아이피 | 127.0.0.1 |

</h6>


#### 6 - 4. 데이터베이스를 생성한다.

###### [STAND ALONE 모드]

<h6>

    $ gcreatedb

</h6>

###### [CLUSTER 모드]

<h6>

    $ gcreatedb --cluster

</h6>

#### 6 - 5. 데이터베이스를 구동한다.

###### [STAND ALONE 모드]

<h6>

    $ gsql sys gliese --as sysdba
    gSQL> STARTUP

</h6>

###### [CLUSTER 모드]

<h6>

    $ gsql sys gliese --as sysdba
    gSQL> STARTUP
    gSQL> ALTER SYSTEM OPEN GLOBAL DATABASE;

</h6>

###### 데이터베이스가 마스터 노드인 경우

<h6>

    쿼리> CREATE CLUSTER GROUP <그룹 명> CLUSTER MEMBER <멤버 명> HOST <서버 아이피> PORT <클러스터 포트>;
    gSQL> CREATE CLUSTER GROUP G1 CLUSTER MEMBER G1N1 HOST '192.168.0.50' PORT 10101;

</h6>

###### 데이터베이스가 슬레이브 노드인 경우

<h6>

    # 쿼리를 마스터 노드에서 수행한다.

    # 새로운 그룹을 추가하는 경우
    쿼리> CREATE CLUSTER GROUP <그룹 명> CLUSTER MEMBER <멤버 명> HOST <서버 아이피> PORT <클러스터 포트>;
    gSQL> CREATE CLUSTER GROUP G2 CLUSTER MEMBER G1N1 HOST '192.168.0.50' PORT 10101;

    # 기존 그룹에 멤버를 추가하는 경우
    쿼리> ALTER CLUSTER GROUP <그룹 명> ADD CLUSTER MEMBER <멤버 명> HOST <서버 아이피> PORT <클러스터 포트>;
    gSQL> ALTER CLUSTER GROUP G1 ADD CLUSTER MEMBER G1N1 HOST '192.168.0.50' PORT 10101;

</h6>

#### 6 - 6. 데이터베이스 스키마를 구축한다.

###### [STAND ALONE 모드]

<h6>

    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/standalone/DictionarySchema.sql
    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/standalone/InformationSchema.sql
    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/standalone/PerformanceViewSchema.sql

</h6>

###### [CLUSTER 모드]

<h6>

    # 최초 마스터 노드에 대해서 한번만 수행하면 된다.

    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/cluster/DictionarySchema.sql
    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/cluster/InformationSchema.sql
    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/cluster/PerformanceViewSchema.sql

</h6>

## 7. 리스너 구동

#### 7 - 1. 리스너를 구동한다.

<h6>

    $ glsnr --start

</h6>

#### 7 - 2. 리스너 상태를 조회한다.

<h6>

    $ glsnr --status

</h6>

#### 7 - 3. 리스너를 종료한다.

<h6>

    $ glsnr --stop
</h6>

## 8. 테이블스페이스 및 유저 생성

#### 8 - 1. DATA 테이블스페이스를 생성한다.

###### [테이블스페이스 생성]

<h6>

    쿼리> CREATE TABLESPACE <테이블스페이스 명> DATAFILE <데이터파일 명> SIZE <크기>;
    gSQL> CREATE TABLESPACE SAMPLE_DATA_TBS DATAFILE 'sample_data_01.dbf' SIZE 1G;

</h6>

###### [데이터파일 추가]

<h6>

    쿼리> ALTER TABLESPACE <테이블스페이스 명> ADD DATAFILE <데이터파일 명> SIZE <크기>;
    gSQL> ALTER TABLESPACE SAMPLE_DATA_TBS ADD DATAFILE 'sample_data_02.dbf' SIZE 1G;

</h6>

#### 8 - 2. TEMP 테이블스페이스를 생성한다.

###### [테이블스페이스 생성]

<h6>

    쿼리> CREATE TEMPORARY TABLESPACE <테이블스페이스 명> MEMORY <데이터파일 명> SIZE <크기>;
    gSQL> CREATE TABLESPACE SAMPLE_TEMP_TBS MEMORY 'sample_temp_01' SIZE 1G;

</h6>

###### [데이터파일 추가]

<h6>

    쿼리> ALTER TABLESPACE <테이블스페이스 명> ADD MEMORY <데이터파일 명> SIZE <크기>;
    gSQL> ALTER TABLESPACE SAMPLE_TEMP_TBS ADD MEMORY 'sample_temp_02' SIZE 1G;

</h6>

#### 8 - 3. 사용자를 생성한다.

<h6>

    쿼리> CREATE USER <사용자 명> IDENTIFIED BY <사용자 비밀번호> DEFAULT TABLESPACE <DATA 테이블스페이스 명> TEMPORARY TABLESPACE <TEMP 테이블스페이스 명>;
    gSQL> CREATE USER SAMPLE IDENTIFIED BY sample DEFAULT TABLESPACE SAMPLE_DATA_TBS TEMPORARY TABLESPACE SAMPLE_TEMP_TBS;

</h6>

#### 8 - 4. 생성한 사용자에 권한을 부여한다.

<h6>

    # 전체 권한 부여
    쿼리> GRANT ALL ON DATABASE TO <사용자 명>;
    gSQL> GRANT ALL ON DATABASE TO SAMPLE;

</h6>

#### 8 - 5. 생성한 사용자로 데이터베이스에 접속한다.

<h6>

    $ gsql SAMPLE sample
    gSQL>

</h6>
