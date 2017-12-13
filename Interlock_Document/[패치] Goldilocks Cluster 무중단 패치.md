# Goldilocks Cluster 무중단 패치 가이드

## 1. 개요

#### 1 - 1. 무중단 환경에서 Goldilocks Cluster 를 패치하는 방법을 설명한다.

#### 1 - 2. Goldilocks Cluster 에 대한 기본 지식을 습득한 상태를 가정하고 진행한다.

#### 1 - 3. 패치 도중 하나 이상의 인스턴스가 SHUTDOWN 된 경우 DDL 작업이 이뤄지지 않는다.

#### 1 - 4. 패치 시, 데이터베이스 메이저 버전(x.x.y 의 x 를 의미)이 변경된 경우 이 문서의 패치 절차를 따라서는 안된다.


###### [작업 환경]

<h6>

| 패치 전 버전 | 패치 후 버전 | MEMBER | HOST | PORT |
|:--           |:--           |:--     |:--   |:--   |
| 3.1.1        | 3.1.2        | G1N1   | 192.168.0.113 | 10101 |
| 3.1.1        | 3.1.2        | G1N2   | 192.168.0.114 | 10101 |
| 3.1.1        | 3.1.2        | G2N1   | 192.168.0.119 | 10101 |
| 3.1.1        | 3.1.2        | G2N2   | 192.168.0.120 | 10101 |

</h6>

<h6>

| MEMBER | 역할 |
|:--     |:--   |
| G1N1   | 그룹 G1 의 Master |
| G1N2   | 그룹 G1 의 Slave ( G1N1 의 Cloned Instance ) |
| G2N1   | 그룹 G2 의 Master |
| G2N2   | 그룹 G2 의 Slave ( G2N1 의 Cloned Instance ) |

</h6>

## 2. 시작 전 체크사항

#### 2 - 1. 모든 데이터베이스가 구동 중인지 확인한다.


<h6>

    gSQL> SELECT ORIGIN_MEMBER_NAME, RELEASE_VERSION, INSTANCE_STATUS FROM GV$INSTANCE;

    ORIGIN_MEMBER_NAME RELEASE_VERSION                     INSTANCE_STATUS
    ------------------ ----------------------------------- ---------------
    G1N1               Release Venus.3.1.1 revision(23756) OPEN
    G2N2               Release Venus.3.1.1 revision(23756) OPEN
    G2N1               Release Venus.3.1.1 revision(23756) OPEN
    G1N2               Release Venus.3.1.1 revision(23756) OPEN

    4 rows selected.

</h6>

## 3. 패치 진행

#### 3 - 1. G2N2 인스턴스의 버전을 3.1.1 에서 3.1.2 로 변경한다.

###### 1. G2N2 에 들어오는 사용자세션을 G2N1 쪽으로 절체한다.

###### 2. G2N2 에 로컬세션이 있는지 확인한다.

<h6>

    gSQL> SELECT
            'CLIENT_CONNECTED_SESSION' SESSION_TYPE,
            COUNT(*) SESSION_COUNT
          FROM X$SESSION@LOCAL
            WHERE LOGIN_USER_ID != 0 AND ID != 1 AND STATUS = 'CONNECTED' AND PROGRAM != 'cluster peer';

    SESSION_TYPE             SESSION_COUNT
    ------------------------ -------------
    CLIENT_CONNECTED_SESSION             1

    1 row selected.

</h6>

###### 3. G2N2 를 종료한다.

<h6>

    gSQL> SHUTDOWN ABORT;
    Shutdown success

</h6>

###### 4. 공유메모리 및 프로세스를 조회한다.

<h6>

    $ ipcs -a | grep $LOGNAME
    $ ps -ef | grep $LOGNAME

</h6>

###### 5. 현재 버전의 goldilocks_home 디렉토리를, 패치 버전으로 변경한다.
###### 6. license 파일을 패치 버전의 license 경로에 복사한다.

<h6>

    $ ls
    goldilocks_home goldilocks_home_3.1.2
    $ mv goldilocks_home goldilocks_home_3.1.1
    $ mv goldilocks_home_3.1.2 goldilocks_home

    $ cp goldilocks_home_3.1.1/license/license goldilocks_home/license/license

</h6>

###### 7. G2N2 를 재구동 한다.

<h6>

    gSQL> STARTUP;
    Startup success

    gSQL> ALTER SYSTEM JOIN DATABASE;
    ERR-42000(16405): of the total '4' tables in the database, '1' tables need to be rebalanced [참고1]
    System altered.

    gSQL> ALTER DATABASE REBALANCE;
    Database altered.

</h6>

<h6>

* [참고1] 다음의 메세지는 오류 상황이 아니며, 테이블의 데이터가 변경되었으므로 재배치 구문을 수행하라는 메세지이다.<br/>ERR 구문 아래에 System altered. 가 출력되었으면 정상적으로 JOIN 된 상태이다.

</h6>

###### 8. 모든 데이터베이스가 구동중인지 확인한다.

<h6>

    gSQL> SELECT ORIGIN_MEMBER_NAME, RELEASE_VERSION, INSTANCE_STATUS FROM GV$INSTANCE;

    ORIGIN_MEMBER_NAME RELEASE_VERSION                     INSTANCE_STATUS
    ------------------ ----------------------------------- ---------------
    G1N1               Release Venus.3.1.1 revision(23756) OPEN
    G2N2               Release Venus.3.1.2 revision(23930) OPEN
    G2N1               Release Venus.3.1.1 revision(23756) OPEN
    G1N2               Release Venus.3.1.1 revision(23756) OPEN

    4 rows selected.

</h6>

#### 3 - 2. G2N1 의 인스턴스의 버전을 3.1.1 에서 3.1.2 로 변경한다.

###### * [참고사항] 작업 내역은 위의 내용과 동일하다.

###### 1. G2N1 에 들어오는 사용자세션을 G2N2 쪽으로 절체한다.

###### 2. G2N1 에 로컬세션이 있는지 확인한다.

<h6>

    gSQL> SELECT
            'CLIENT_CONNECTED_SESSION' SESSION_TYPE,
            COUNT(*) SESSION_COUNT
          FROM X$SESSION@LOCAL
            WHERE LOGIN_USER_ID != 0 AND ID != 1 AND STATUS = 'CONNECTED' AND PROGRAM != 'cluster peer';

    SESSION_TYPE             SESSION_COUNT
    ------------------------ -------------
    CLIENT_CONNECTED_SESSION             1

    1 row selected.

</h6>

###### 3. G2N1 을 종료한다.

<h6>

    gSQL> SHUTDOWN ABORT;
    Shutdown success

</h6>

###### 4. 공유메모리 및 프로세스를 조회한다.

<h6>

    $ ipcs -a | grep $LOGNAME
    $ ps -ef | grep $LOGNAME

</h6>

###### 5. 현재 버전의 goldilocks_home 디렉토리를, 패치 버전으로 변경한다.
###### 6. license 파일을 패치 버전의 license 경로에 복사한다.

<h6>

    $ ls
    goldilocks_home goldilocks_home_3.1.2
    $ mv goldilocks_home goldilocks_home_3.1.1
    $ mv goldilocks_home_3.1.2 goldilocks_home

    $ cp goldilocks_home_3.1.1/license/license goldilocks_home/license/license

</h6>

###### 7. G2N1 을 재구동 한다.

<h6>

    gSQL> STARTUP;
    Startup success

    gSQL> ALTER SYSTEM JOIN DATABASE;
    ERR-42000(16405): of the total '4' tables in the database, '1' tables need to be rebalanced [참고1]
    System altered.

    gSQL> ALTER DATABASE REBALANCE;
    Database altered.

</h6>

<h6>

* [참고1] 다음의 메세지는 오류 상황이 아니며, 테이블의 데이터가 변경되었으므로 재배치 구문을 수행하라는 메세지이다.<br/>ERR 구문 아래에 System altered. 가 출력되었으면 정상적으로 JOIN 된 상태이다.

</h6>

###### 8. 모든 데이터베이스가 구동중인지 확인한다.

<h6>

    gSQL> SELECT ORIGIN_MEMBER_NAME, RELEASE_VERSION, INSTANCE_STATUS FROM GV$INSTANCE;

    ORIGIN_MEMBER_NAME RELEASE_VERSION                     INSTANCE_STATUS
    ------------------ ----------------------------------- ---------------
    G1N1               Release Venus.3.1.1 revision(23756) OPEN
    G2N2               Release Venus.3.1.2 revision(23930) OPEN
    G1N2               Release Venus.3.1.1 revision(23756) OPEN
    G2N1               Release Venus.3.1.2 revision(23930) OPEN

    4 rows selected.

</h6>

#### 3 - 3. G1N2 의 인스턴스의 버전을 3.1.1 에서 3.1.2 로 변경한다.

###### * [참고사항] 작업 내역은 위의 내용과 동일하다.

###### 1. G1N2 에 들어오는 사용자세션을 G1N1 쪽으로 절체한다.

###### 2. G1N2 에 로컬세션이 있는지 확인한다.

<h6>

    gSQL> SELECT
            'CLIENT_CONNECTED_SESSION' SESSION_TYPE,
            COUNT(*) SESSION_COUNT
          FROM X$SESSION@LOCAL
            WHERE LOGIN_USER_ID != 0 AND ID != 1 AND STATUS = 'CONNECTED' AND PROGRAM != 'cluster peer';

    SESSION_TYPE             SESSION_COUNT
    ------------------------ -------------
    CLIENT_CONNECTED_SESSION             1

    1 row selected.

</h6>

###### 3. G1N2 를 종료한다.

<h6>

    gSQL> SHUTDOWN ABORT;
    Shutdown success

</h6>

###### 4. 공유메모리 및 프로세스를 조회한다.

<h6>

    $ ipcs -a | grep $LOGNAME
    $ ps -ef | grep $LOGNAME

</h6>

###### 5. 현재 버전의 goldilocks_home 디렉토리를, 패치 버전으로 변경한다.
###### 6. license 파일을 패치 버전의 license 경로에 복사한다.

<h6>

    $ ls
    goldilocks_home goldilocks_home_3.1.2
    $ mv goldilocks_home goldilocks_home_3.1.1
    $ mv goldilocks_home_3.1.2 goldilocks_home

    $ cp goldilocks_home_3.1.1/license/license goldilocks_home/license/license

</h6>

###### 7. G1N2 를 재구동 한다.

<h6>

    gSQL> STARTUP;
    Startup success

    gSQL> ALTER SYSTEM JOIN DATABASE;
    ERR-42000(16405): of the total '4' tables in the database, '1' tables need to be rebalanced [참고1]
    System altered.

    gSQL> ALTER DATABASE REBALANCE;
    Database altered.

</h6>

<h6>

* [참고1] 다음의 메세지는 오류 상황이 아니며, 테이블의 데이터가 변경되었으므로 재배치 구문을 수행하라는 메세지이다.<br/>ERR 구문 아래에 System altered. 가 출력되었으면 정상적으로 JOIN 된 상태이다.

</h6>

###### 8. 모든 데이터베이스가 구동중인지 확인한다.

<h6>

    gSQL> SELECT ORIGIN_MEMBER_NAME, RELEASE_VERSION, INSTANCE_STATUS FROM GV$INSTANCE;

    ORIGIN_MEMBER_NAME RELEASE_VERSION                     INSTANCE_STATUS
    ------------------ ----------------------------------- ---------------
    G1N1               Release Venus.3.1.1 revision(23756) OPEN
    G2N2               Release Venus.3.1.2 revision(23930) OPEN
    G2N1               Release Venus.3.1.2 revision(23930) OPEN
    G1N2               Release Venus.3.1.2 revision(23930) OPEN

    4 rows selected.

</h6>

#### 3 - 4. G1N1 의 인스턴스의 버전을 3.1.1 에서 3.1.2 로 변경한다.

###### * [참고사항] 작업 내역은 위의 내용과 동일하다.

###### 1. G1N1 에 들어오는 사용자세션을 G1N2 쪽으로 절체한다.

###### 2. G1N1 에 로컬세션이 있는지 확인한다.

<h6>

    gSQL> SELECT
            'CLIENT_CONNECTED_SESSION' SESSION_TYPE,
            COUNT(*) SESSION_COUNT
          FROM X$SESSION@LOCAL
            WHERE LOGIN_USER_ID != 0 AND ID != 1 AND STATUS = 'CONNECTED' AND PROGRAM != 'cluster peer';

    SESSION_TYPE             SESSION_COUNT
    ------------------------ -------------
    CLIENT_CONNECTED_SESSION             1

    1 row selected.

</h6>

###### 3. G1N1 을 종료한다.

<h6>

    gSQL> SHUTDOWN ABORT;
    Shutdown success

</h6>

###### 4. 공유메모리 및 프로세스를 조회한다.

<h6>

    $ ipcs -a | grep $LOGNAME
    $ ps -ef | grep $LOGNAME

</h6>

###### 5. 현재 버전의 goldilocks_home 디렉토리를, 패치 버전으로 변경한다.
###### 6. license 파일을 패치 버전의 license 경로에 복사한다.

<h6>

    $ ls
    goldilocks_home goldilocks_home_3.1.2
    $ mv goldilocks_home goldilocks_home_3.1.1
    $ mv goldilocks_home_3.1.2 goldilocks_home

    $ cp goldilocks_home_3.1.1/license/license goldilocks_home/license/license

</h6>

###### 7. G1N1 을 재구동 한다.

<h6>

    gSQL> STARTUP;
    Startup success

    gSQL> ALTER SYSTEM JOIN DATABASE;
    ERR-42000(16405): of the total '4' tables in the database, '1' tables need to be rebalanced [참고1]
    System altered.

    gSQL> ALTER DATABASE REBALANCE;
    Database altered.

</h6>

<h6>

* [참고1] 다음의 메세지는 오류 상황이 아니며, 테이블의 데이터가 변경되었으므로 재배치 구문을 수행하라는 메세지이다.<br/>ERR 구문 아래에 System altered. 가 출력되었으면 정상적으로 JOIN 된 상태이다.

</h6>

###### 8. 모든 데이터베이스가 구동중인지 확인한다.

<h6>

    gSQL> SELECT ORIGIN_MEMBER_NAME, RELEASE_VERSION, INSTANCE_STATUS FROM GV$INSTANCE;

    ORIGIN_MEMBER_NAME RELEASE_VERSION                     INSTANCE_STATUS
    ------------------ ----------------------------------- ---------------
    G1N1               Release Venus.3.1.2 revision(23930) OPEN
    G2N2               Release Venus.3.1.2 revision(23930) OPEN
    G2N1               Release Venus.3.1.2 revision(23930) OPEN
    G1N2               Release Venus.3.1.2 revision(23930) OPEN

    4 rows selected.

</h6>
