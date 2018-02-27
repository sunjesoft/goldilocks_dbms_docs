# 핫백업, 시나리오별 복구 방법

## 1. 서론

#### 1 - 1. 테스트 환경

 <h6>

 1. 운영체제 버전　　　CentOS 7
 2. 커널 버전　　　　　3.10.0-327.el7.x86_64
 3. 데이터베이스 버전　3.1.7
 4. 기본 설치　　　　　프로퍼티 변경 X ( 아카이브 모드 예외 )
 5. 모든 리눅스 명령어는 goldilocks_data 경로에서 실행

 </h6>

 #### 1 - 2. 시작하기 앞서

 <h6>

 1. CURRENT 리두 로그 파일이 유실된 경우, CURRENT 리두 로그 파일에 있던 데이터는 복구가 불가능하다.
 2. 백업된 파일의 마지막 LSN 은, 아카이브 혹은 리두 로그 파일 내에 있어야 한다.
 3. UNTIL CHANGE 복구가 가능은 하지만, 특정 데이터까지의 복구를 위한 LSN 의 데이터 값 확인은 복잡한 방법이므로 권고하지 않는다.
 4. UNTIL CHANGE 복구시 리두 로그 파일에 COMMIT 되지 않은 LSN 이 있더라도, 이 LSN 값에 대해서는 복구가 불가능하다.
 5. 아카이브 로그 파일이 존재하지 않은 상황에서, 리두로그 파일을 모두 유실하는 경우 복구가 불가능하다.

 </h6>

 ## 2. 핫백업 ( 온라인 백업 )

 ###### [Step 1]. 데이터베이스를 백업모드로 변경한다.
 ###### [Step 2]. log, wal, dbf 파일을 백업경로로 물리적으로 복사한다.
 ###### [Step 3]. 데이터베이스의 백업모드를 종료한다.

<h6>

     gSQL> ALTER DATABASE BEGIN BACKUP;

     $ cp db/* wal/*                    backup/       (필수)
     $ cp conf/* archive_log/* trc/*    backup/       (선택)

     gSQL> ALTER DATABASE END BACKUP;

</h6>

## 3. 핫백업(온라인 백업)을 이용한 시나리오별 복구 방법

#### Scenario 3 - 1. 데이터 파일을 유실한 경우

#### Scenario 3 - 2. 컨트롤 파일을 유실한 경우

#### Scenario 3 - 3. 리두 로그 파일을 유실한 경우

#### Scenario 3 - 4. 컨트롤 파일과 데이터 파일을 유실한 경우

#### Scenario 3 - 5. 컨트롤 파일과 리두 로그 파일을 유실한 경우

#### Scenario 3 - 6. 데이터 파일과 리두 로그 파일을 유실한 경우

#### Scenario 3 - 7. 모든 파일을 유실한 경우


## 3. 핫백업(온라인 백업)을 이용한 시나리오별 복구 방법

#### Scenario 3 - 1. 데이터 파일을 유실한 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>
    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 데이터파일 삭제

<h6>

    $ rm db/system_data.dbf
    $ rm db/system_dict.dbf
    $ rm db/system_undo.dbf

</h6>

###### [Step 7]. 백업된 데이터 파일을 db/ 경로에 복사

<h6>

    $ cp backup/system_data.dbf db/
    $ cp backup/system_dict.dbf db/
    $ cp backup/system_undo.dbf db/

</h6>

###### [Step 8]. 데이터베이스 복구 및 구동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE RECOVER;
    gSQL> ALTER SYSTEM OPEN DATABASE;

</h6>

###### [Step 9]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST
     3 2018-02-10 HOT BACKUP TEST

</h6>


#### Scenario 3 - 2. 컨트롤 파일을 유실한 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>

    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 컨트롤 파일 삭제

<h6>

    $ rm wal/control_0.ctl
    $ rm wal/control_1.ctl

</h6>

###### [Step 7]. 백업된 컨트롤 파일을 wal/ 경로에 복사

<h6>

    $ cp backup/control_0.ctl wal/
    $ cp backup/control_1.ctl wal/

</h6>

###### [Step 8]. 데이터베이스 복구 및 기동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE RECOVER '/home/sh91/GOLDILOCKS_data/wal/redo_2_0.log';
          ‐‐> 위 단계 생략시 current 로그 파일의 데이터 유실
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;

</h6>

###### [Step 9]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST
     3 2018-02-10 HOT BACKUP TEST

</h6>


#### Scenario 3 - 3. 리두 로그 파일을 유실한 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>

    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 리두로그 파일 삭제

<h6>

    $ rm wal/redo_0_0.log
    $ rm wal/redo_1_0.log
    $ rm wal/redo_2_0.log
    $ rm wal/redo_3_0.log

</h6>

###### [Step 7]. 아카이브 로그파일을 리두 로그파일로 복원

<h6>

    $ cp archive_log/archive_5.log wal/redo_1_0.log
    $ cp archive_log/archive_4.log wal/redo_0_0.log
    $ cp archive_log/archive_3.log wal/redo_3_0.log
    $ cp archive_log/archive_2.log wal/redo_2_0.log

</h6>

###### [Step 8]. 데이터 파일, 컨트롤 파일 삭제

<h6>

    $ rm db/system_data.dbf
    $ rm db/system_dict.dbf
    $ rm db/system_undo.dbf
    $ rm wal/control_0.ctl
    $ rm wal/control_1.ctl

</h6>

###### [Step 9]. 백업된 데이터 파일과 컨트롤 파일을 각각 db/, wal/ 경로에 복사

<h6>

    $ cp backup/system_data.dbf db/
    $ cp backup/system_dict.dbf db/
    $ cp backup/system_undo.dbf db/
    $ cp backup/control_0.ctl   wal/
    $ cp backup/control_1.ctl   wal/

</h6>

###### [Step 10]. 데이터베이스 복구 및 구동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;

</h6>

###### [Step 11]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST

</h6>


#### Scenario 3 - 4. 컨트롤 파일과 데이터 파일을 유실한 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>

    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 컨트롤 파일과 데이터 파일 삭제

<h6>

    $ rm db/system_data.dbf
    $ rm db/system_dict.dbf
    $ rm db/system_undo.dbf
    $ rm wal/control_0.ctl
    $ rm wal/control_1.ctl

</h6>

###### [Step 7]. 백업된 데이터 파일과 컨트롤 파일을 각각 db/, wal/ 경로에 복사

<h6>

    $ cp backup/system_data.dbf db/
    $ cp backup/system_dict.dbf db/
    $ cp backup/system_undo.dbf db/
    $ cp backup/control_0.ctl   wal/
    $ cp backup/control_1.ctl   wal/

</h6>

###### [Step 8]. 데이터베이스 복구 및 기동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE RECOVER '/home/sh91/GOLDILOCKS_data/wal/redo_2_0.log';
          ‐‐> 위 단계 생략시 current 로그 파일의 데이터 유실
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;

</h6>

###### [Step 9]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST
     3 2018-02-10 HOT BACKUP TEST

</h6>


#### Scenario 3 - 5. 컨트롤 파일과 리두 로그 파일을 유실한 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>

    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 컨트롤 파일과 리두로그 파일 삭제

<h6>

    $ rm wal/control_0.ctl
    $ rm wal/control_1.ctl
    $ rm wal/redo_0_0.log
    $ rm wal/redo_1_0.log
    $ rm wal/redo_2_0.log
    $ rm wal/redo_3_0.log

</h6>

###### [Step 7]. 아카이브 로그파일을 리두 로그파일로 복원

<h6>

    $ cp archive_log/archive_5.log wal/redo_1_0.log
    $ cp archive_log/archive_4.log wal/redo_0_0.log
    $ cp archive_log/archive_3.log wal/redo_3_0.log
    $ cp archive_log/archive_2.log wal/redo_2_0.log

</h6>

###### [Step 8]. 백업된 컨트롤 파일을 wal/ 경로에 복사

<h6>

    $ cp backup/control_0.ctl   wal/
    $ cp backup/control_1.ctl   wal/

</h6>

###### [Step 9]. 데이터 파일 삭제

<h6>

    $ rm db/system_data.dbf
    $ rm db/system_dict.dbf
    $ rm db/system_undo.dbf

</h6>

###### [Step 10]. 백업된 데이터 파일을 db/ 경로에 복사

<h6>

    $ cp backup/system_data.dbf db/
    $ cp backup/system_dict.dbf db/
    $ cp backup/system_undo.dbf db/

</h6>

###### [Step 11]. 데이터베이스 복구 및 구동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;

</h6>

###### [Step 12]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST

</h6>


#### Scenario 3 - 6. 데이터 파일과 리두 로그 파일을 유실한 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>

    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 데이터 파일과 리두로그 파일 삭제

<h6>

    $ rm wal/redo_0_0.log
    $ rm wal/redo_1_0.log
    $ rm wal/redo_2_0.log
    $ rm wal/redo_3_0.log
    $ rm db/system_data.dbf
    $ rm db/system_dict.dbf
    $ rm db/system_undo.dbf

</h6>

###### [Step 7]. 아카이브 로그파일을 리두 로그파일로 복원

<h6>

    $ cp archive_log/archive_5.log wal/redo_1_0.log
    $ cp archive_log/archive_4.log wal/redo_0_0.log
    $ cp archive_log/archive_3.log wal/redo_3_0.log
    $ cp archive_log/archive_2.log wal/redo_2_0.log

</h6>

###### [Step 8]. 백업된 데이터 파일을 db/ 경로에 복사

<h6>

    $ cp backup/system_data.dbf db/
    $ cp backup/system_dict.dbf db/
    $ cp backup/system_undo.dbf db/

</h6>

###### [Step 9]. 데이터베이스 복구 및 구동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;

</h6>

###### [Step 10]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST

</h6>


#### Secnario 3 - 7. 모든 파일이 유실되는 경우

###### [Step 1]. 백업전 데이터 적재

<h6>

    gSQL> CREATE TABLE HOT_TEST ( C1 INT, C2 DATE, C3 VARCHAR(50) );
    gSQL> INSERT INTO HOT_TEST VALUES (1, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

</h6>

###### [Step 2]. 온라인 백업 수행

<h6>

    gSQL> ALTER DATABASE BEGIN BACKUP;

    $ cp db/*  backup/
    $ cp wal/* backup/

    gSQL> INSERT INTO HOT_TEST VALUES (2, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;

    gSQL> ALTER DATABASE END BACKUP;

</h6>

###### [Step 3]. 아카이브 파일 생성 ( 테스트를 위한 작업 - 생략 가능 )

<h6>

    gSQL> ALTER SYSTEM SWITCH LOGFILE; (x6)

</h6>

###### [Step 4]. 2차 데이터 적재

<h6>

    gSQL> INSERT INTO HOT_TEST VALUES (3, sysdate, 'HOT BACKUP TEST');
    gSQL> COMMIT;
    gSQL> INSERT INTO HOT_TEST VALUES (4, sysdate, 'HOT BACKUP TEST');

</h6>

###### [Step 5]. 데이터베이스 비정상 종료

<h6>

    gSQL> SHUTDOWN ABORT

</h6>

###### [Step 6]. 컨트롤 파일, 데이터 파일, 리두로그 파일 삭제

<h6>

    $ rm wal/control_0.ctl
    $ rm wal/control_1.ctl
    $ rm wal/redo_0_0.log
    $ rm wal/redo_1_0.log
    $ rm wal/redo_2_0.log
    $ rm wal/redo_3_0.log
    $ rm db/system_dict.dbf
    $ rm db/system_data.dbf
    $ rm db/system_undo.dbf

</h6>

###### [Step 7]. 아카이브 로그파일을 리두 로그파일로 복원

<h6>

    $ cp archive_log/archive_5.log wal/redo_1_0.log
    $ cp archive_log/archive_4.log wal/redo_0_0.log
    $ cp archive_log/archive_3.log wal/redo_3_0.log
    $ cp archive_log/archive_2.log wal/redo_2_0.log

</h6>

###### [Step 8]. 백업된 데이터 파일과 컨트롤 파일을 각각 db/, wal/ 경로에 복사

<h6>

    $ cp backup/control_0.ctl   wal/
    $ cp backup/control_1.ctl   wal/
    $ cp backup/system_data.dbf db/
    $ cp backup/system_dict.dbf db/
    $ cp backup/system_undo.dbf db/

</h6>

###### [Step 9]. 데이터베이스 복구 및 구동

<h6>

    gSQL> STARTUP MOUNT
    gSQL> ALTER DATABASE BEGIN INCOMPLETE RECOVERY;
    gSQL> ALTER DATABASE RECOVER AUTOMATICALLY;
    gSQL> ALTER DATABASE END INCOMPLETE RECOVERY;
    gSQL> ALTER SYSTEM OPEN DATABASE RESETLOGS;

</h6>

###### [Step 10]. 데이터 확인

<h6>

    gSQL> SELECT * FROM HOT_TEST;

    C1 C2         C3
    -- ---------- ---------------
     1 2018-02-10 HOT BACKUP TEST
     2 2018-02-10 HOT BACKUP TEST
</h6>
