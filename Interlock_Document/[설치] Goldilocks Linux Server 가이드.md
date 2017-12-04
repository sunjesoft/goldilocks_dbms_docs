# Goldilocks Server 설치 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS 를 Linux, AIX, HP-UX 환경에서 설치하는 방법을 설명한다.

#### 1 - 2. 연동을 위한 환경 구축은 사용자가 해야 할 사항이며, 선재소프트는 이 부분에 대한 기술지원을 진행하지 않는다.

#### 1 - 3. GOLDILOCKS 를 구동하기 위한 운영체제 별 최소 커널 값은 다음과 같다.

<h6>

| 운영체제 | 커널   |
|:--       |:--     |
| Linux    | 2.6.32 |
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

###### [Linux]

<h6>

    # vi /etc/sysctl.conf
    ...
    kernel.shmmax = 4294967296
    kernel.shmmni = 4096
    kernel.shmall = 1048576
    kernel.sem = 250 32000 100 128

    # sysctl -p

</h6>

###### [AIX]

<h6>

</h6>

###### [HP-UX]

<h6>

</h6>


#### 2 - 3. IPC 제거 관련된 커널 값을 지정한다. ( Linux 3.0.0 이상 )

###### [Linux]

<h6>

    # vi /etc/systemd/logind.conf
    ...
    RemoveIPC=no

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

#### 4 - 1. goldilocks-server-<version>-<os>-<bit>.tar.gz 파일을 압축 해제한다.

<h6>

    $ tar xzf goldilocks-server-*.tar.gz

</h6>

#### 4 - 2. 압축 파일을 풀면 패키지명 폴더 아래 goldilocks_home, goldilocks_data 가 생성된다.

###### goldilocks_home

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

###### goldilocks_data

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

#### 4 - 4. 홈 경로에 심볼릭링크를 생성한다.

<h6>

    $ ln -s goldilocks-server-venus.3.1.1-linux-x86_64/goldilocks_home/ /home/sundb/goldilocks_home
    $ ln -s goldilocks-server-venus.3.1.1-linux-x86_64/goldilocks_data/ /home/sundb/goldilocks_data

</h6>

#### 4 - 5. 라이센스를 등록한다.

###### license 가 없는 경우 technet@sunjesoft.com 에 요청한다.

<h6>

   $ cp license /home/sundb/goldilocks_home/license/license

</h6>

## 5. 사용자 환경설정

#### 5 - 1. .bash_profile 에 GOLDILOCKS 환경변수를 등록 및 적용한다.

<h6>

    $ vi ~/.bash_profile
    export GOLDILOCKS_HOME=$HOME/goldilocks_home
    export GOLDILOCKS_DATA=$HOME/goldilocks_data
    export PATH=$GOLDILOCKS_HOME/bin:$PATH
    export LD_LIBRARY_PATH=$GOLDILOCKS_HOME/lib:$LD_LIBRARY_PATH

    $ . .bash_profile

    $ vi ~/.odbc.ini
    [GOLDILOCKS]
    HOST=127.0.0.1
    PORT=22581

</h6>

## 6. 데이터베이스 생성

#### 5 - 1. os 인코딩을 확인한다.

<h6>

    $ echo $LANG

</h6>

#### 5 - 2. 인코딩에 따라 데이터베이스 생성시 옵션을 부여한다.

<h6>

| 인코딩 | 옵션 |
|:--     |:--   |
| utf8   | --character_set UTF8 |
| euckr  | --character_set UHC |

</h6>

#### 5 - 3. 데이터베이스를 생성한다.

<h6>

    $ gcreatedb --character_set UTF8

</h6>

#### 5 - 4. 데이터베이스를 구동한다.

<h6>

    $ gsql sys gliese --as sysdba
    gSQL> startup

</h6>

#### 5 - 5. 리스너를 구동한다.

<h6>

    $ glsnr --start

</h6>

#### 5 - 6. 데이터베이스 스키마를 구축한다.

<h6>

    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/standalone/DictionarySchema.sql
    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/standalone/InformationSchema.sql
    $ gsql sys gliese --as sysdba $GOLDILOCKS_HOME/admin/standalone/PerformanceViewSchema.sql

</h6>
