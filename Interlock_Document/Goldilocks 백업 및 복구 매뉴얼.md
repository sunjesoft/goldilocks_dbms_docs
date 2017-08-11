### Goldilocks 백업 및 복구 매뉴얼


#### 목차
1. 개요
2. 백업
3. 콜드백업을 이용한 시나리오별 복구 방법
4. 핫백업을 이용한 시나리오별 복구 방법
5. 증분백업을 이용한 시나리오별 복구 방법



#### 1. 개요
* Goldilocks 를 운영하는 도중 백업 방법과, 장애발생시 백업본을 이용한 복구방법을 기술한다.
* 이 문서는 'Goldilocks 관리자'를 대상으로 작성되었다.
* 이 문서는 Goldilocks 3.1.0 standalone 버전을 기반으로 작성되었다.

[ 주의사항 ]
* Goldilocks 는 아카이브 모드로 운영되어야 한다.
* CURRENT 리두 로그 파일이 유실된 경우, 이 파일에 있는 데이터는 복구가 불가능하다.
* 아카이브 로그 혹은 리두 로그 파일 내에 백업된 파일의 마지막 LSN 값이 있어야 한다.
* 이 문서의 모든 명령어는 goldilocks_data 경로에서 수행되었다.
* 백업 및 복구는 관리자 권한으로 수행되어야 한다.


#### 2. 백업
* 오프라인백업(콜드백업)과 온라인 백업(핫 백업, 증분 백업)으로 백업을 수행할 수 있다.


###### 2 - 1. 오프라인 백업 ( 콜드 백업 )

[ Step 1 ]. 데이터베이스를 종료한다.

[ Step 2 ]. wal, db 폴더를 OS 명령어를 사용하여 백업 경로로 복사한다.


    gSQL> \SHUTDOWN IMMEDIATE

    Shell> cp db/* backup/
    Shell> cp wal/* backup/


###### 2 - 2. 온라인 백업 ( 핫 백업 )

[ Step 1 ]. 데이터베이스를 백업모드로 변경한다.


    gSQL> ALTER DATABASE BEGIN BACKUP;


[ Step 2 ]. wal, db 폴더를 OS 명령어를 사용하여 백업 경로로 복사한다.


    Shell> cp db/* backup/
    Shell> cp wal/* backup/

[ Step 3 ]. 백업모드로 변경한 데이터베이스를 복구한다.


    gSQL> ALTER DATABASE END BACKUP;


###### 2 - 3. 온라인 백업 ( 증분 백업 )
* Goldilocks 는 증분백업을 위해 LEVEL 0 ~ 4 까지를 지원한다.
* 최초의 증분백업은 LEVEL 0 으로 전체 데이터베이스를 백업받아야 한다.
* 이후의 증분백업은 LEVEL 을 증가시키면서, 변경된 데이터에 대해서만 증분백업을 수행할 수 있다.

[ Step 1 ]. LEVEL 0 으로 증분백업을 수행한다. 최초에는 LEVEL 0 으로 수행되어야 한다.


    gSQL> ALTER DATABASE BACKUP INCREMENTAL LEVEL 0;

[ Step 2 ]. LEVEL 0 이후의 변경된 데이터만 하고싶은 경우, LEVEL 단계를 높이며 진행한다.


    gSQL> ALTER DATABASE BACKUP INCREMENTAL LEVEL 1;


#### 3. 콜드백업을 이용한 시나리오별 복구 방법

###### 3 - 1. 데이터 파일을 유실한 경우

[ Step 1 ]. 백업전 데이터 적재


    gSQL> CREATE TABLE COLD_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO COLD_TEST VALUES (1, sysdate, 'COLD BACKUP TEST');
    gSQL> COMMIT;


[ Step 2 ]. 오프라인 백업 수행


    gSQL> \SHUTDOWN IMMEDIATE

    Shell> cp db/* backup/
    Shell> cp wal/* backup/


[ Step 3 ]. 아카이브 파일 생성 ( 테스트를 위한 작업이며 생략 가능 )


    gSQL> \STARTUP
    gSQL> INSERT INTO COLD_TEST VALUES (2, sysdate, 'COLD BACKUP TEST');
    gSQL> COMMIT;
    gSQL> ALTER SYSTEM SWITCH LOGFILE; -- (x6)


[ Step 4 ]. 2차 데이터 적재


    gSQL> INSERT INTO COLD_TEST VALUES (3, sysdate, 'COLD BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO COLD_TEST VALUES (4, sysdate, 'COLD BACKUP TEST');


[ Step 5 ]. 데이터베이스 비정상 종료


    gSQL> \SHUTDOWN ABORT


[ Step 6 ]. 데이터파일 삭제


    Shell> rm db/system_data.dbf
    Shell> rm db/system_dict.dbf
    Shell> rm db/system_undo.dbf


[ Step 7 ]. 백업된 데이터 파일을 db/ 경로에 복사


    Shell> cp backup/system_data.dbf db/
    Shell> cp backup/system_dict.dbf db/
    Shell> cp backup/system_undo.dbf db/

[ Step 8 ]. 데이터베이스 복구 및 구동


    gSQL> \STARTUP MOUNT
    gSQL> ALTER DATABASE RECOVER;
    gSQL> ALTER SYSTEM OPEN DATABASE;
    
