# gloader

## 1. 개요

#### 1 - 1. Goldilocks 에 존재하는 데이터를 테이블 단위로 업/다운로드 할 수 있는 프로그램이다.

#### 1 - 2. DA 모드는 gloader, TCP 모드는 gloadernet 을 사용해야한다.


![gloader_01](https://user-images.githubusercontent.com/9734988/36660755-a0028016-1b1c-11e8-95df-e70b3c3bd76f.JPG)

#### 1 - 3. 업로드시 Control 과 Data 파일이 필요하며 결과로 Log File 과 Bad File 이 생성된다.

#### 1 - 4. 다운로드시 Control 파일이 필요하며 결과로 Data File 과 Log File 이 생성된다.


## 2. gloader File 역할

#### 2 - 1. Control File

###### gloader 가 동작하는데 필요한 옵션을 지닌 파일이다.

###### [ 필수 정보 ]

<h6>

* SCHEMA.TABLE 이름

</h6>



###### [ 선택 정보 ]

<h6>

* COLUMN 간의 구분자
* COLUMN 데이터의 시작과 끝을 알리는 문자
* ROW 간의 구분자
* CHARACTER SET
* 공백문자에 대한 Trim 여부

</h6>


###### 각 정보별 구문과 DEFAULT 값은 다음과 같다.

<h6>

| 정보 | 구문 | 기본값 |
| :--  | :--  | :--    |
| SCHEMA.TABLE 이름 | TABLE | |
| COLUMN 간의 구분자 | FIELDS TERMINATED BY | , |
| COLUMN 데이터의 시작과 끝을 알리는 문자 | OPTIONARLLY ENCLOSED BY | NULL |
| ROW 간의 구분자 | LINES TERMINATED BY | \n |
| CHARACTER SET | CHARACTERSET | UTF8 |
| 공백문자에 대한 Trim 여부 | RTRIM | OFF |
| | LTRIM | OFF |

</h6>

#### 2 - 2. Data File

###### Control File 에서 지정한 문법으로 데이터가 출력/적재된다.

###### 텍스트 형식과 바이너리 형식을 지원한다.


| 형식      | 장단점 |
| :--       | :--    |
| 텍스트    | 파일의 내용을 수정할 수 있으나, 바이너리 형식에 비해 속도가 느림 |
| 바이너리  | 파일의 내용을 수정할 수 없으나, 텍스트 형식에 비해 속도가 빠름  |

#### 2 - 3. Log File

###### gloader 가 동작중 발생되는 에러와 결과를 기록한다.

<h6>

* Data File 의 에러가 발생된 줄과 원인
* gloader 운영에 대한 결과

</h6>

#### 2 - 4. Bad File

###### 데이터 업로드 중 에러가 발생한 데이터를 기록한다.

###### 다음과 같은 경우 업로드가 실패할 수 있다.

<h6>

* 중복 제약
* 타입 포멧
* 용량 부족
* Control File 과 문법 불일치

</h6>

## 3. gloader 옵션


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
        -T, --tablename                         [schema_name.]table_name
        -F, --fieldterm                         field terminator
        -L, --lineterm                          line terminator
        -q, --qualifier                         qualifier(column data encloser)
        -n  --no-copyright                      suppresses the display of the banner
        -s, --silent                            suppresses the display of the result message

    help:

        -h, --help                              print help message


<h6>

| 대분류 | 중분류 | 설명 |
| :--    | :--    | :--  |
| mode   | -e     | 데이터 추출 모드 |
|        | -i     | 데이터 적재 모드 |
| control | -c FILE | Control File 명을 입력 |
| data | -d FILE | Data File 명을 입력 |
| format | -f TEXT | 데이터파일이 텍스트 형식으로 저장 |
|        | -f BINARY | 데이터파일이 바이너리 형식으로 저장 |
| options | -l FILE | Log File 명을 입력 |
|         | -b FILE | Bad File 명을 입력 |
|         | -d DSN  | DSN 명을 입력 (기본값은 GOLDILOCKS)|
|         | -c COMMIT | 적재 시 COMMIT 주기 |
|         | -p NUMBER | 수행 시 병렬작업 수 |

</h6>


## 4. gloader 사용예제

#### 4 - 1. 데이터 다운로드

###### PUBLIC.T1 의 테이블 데이터를 조회한다.

<h6>

    gSQL> SELECT * FROM PUBLIC.T1;

      C1   C2
    ---- ----
       1    1
       2    2
    null    3
       4 null

    4 rows selected.

</h6>

###### Control File 을 작성한다.

* 기본값을 사용하는 경우 T1.ctl 의 TABLE 하위 구문은 작성하지 않아도 가능하다.

<h6>

    $ cat t1.ctl
    TABLE PUBLIC.T1
    FIELDS TERMINATED BY ','
    OPTIONALLY ENCLOSED BY '"'
    LINES TERMINATED BY '\n'
    CHARACTERSET UTF8
    RTRIM OFF
    LTRIM OFF

</h6>

###### gloader 를 사용하여 다운로드를 수행한다.

<h6>

    $ gloader TEST test -e -c t1.ctl -d t1.dat

     Copyright © 2010 SUNJESOFT Inc. All rights reserved.
     Release Venus.3.1.5 revision(24371)

     COMPLETED IN EXPORTING TABLE: PUBLIC.T1, 4 RECORDS

</h6>

###### 생성된 Data File 을 조회한다.

<h6>

    $ cat t1.dat
    "1","1"
    "2","2"
    "","3"
    "4",""

</h6>

###### 생성된 Log File 을 조회한다.

<h6>

    $ cat t1.log
    COMPLETED IN EXPORTING TABLE: PUBLIC.T1, 4 RECORDS [ Start Time: 2018-2-26 18:35:9 End Time: 2018-2-26 18:35:9 Taken Time: 79219 micro-sec ]

</h6>

#### 4 - 2. 데이터 업로드

###### PUBLIC.T1 의 테이블 데이터를 조회한다.

<h6>

    gSQL> SELECT * FROM PUBLIC.T1;

    no rows selected.

</h6>

###### 적재하려는 Data File 을 조회한다.

<h6>

    $ cat t1.dat
    "1","1"
    "2","2"
    "","3"
    "4",""

</h6>

###### Control File 을 작성한다.

* 기본값을 사용하는 경우 T1.ctl 의 TABLE 하위 구문은 작성하지 않아도 가능하다.

<h6>

    $ cat t1.ctl
    TABLE PUBLIC.T1
    FIELDS TERMINATED BY ','
    OPTIONALLY ENCLOSED BY '"'
    LINES TERMINATED BY '\n'
    CHARACTERSET UTF8
    RTRIM OFF
    LTRIM OFF

</h6>

###### gloader 를 사용하여 업로드를 수행한다.

<h6>

    $ gloader TEST test -i -c t1.ctl -d t1.dat

    Copyright © 2010 SUNJESOFT Inc. All rights reserved.
    Release Venus.3.1.5 revision(24371)

    COMPLETED IN IMPORTING TABLE: PUBLIC.T1, TOTAL 4 RECORDS, SUCCEEDED 4 RECORDS

</h6>

###### 테이블 데이터를 조회한다.

<h6>

    gSQL> SELECT * FROM PUBLIC.T1;

      C1   C2
    ---- ----
       1    1
       2    2
    null    3
       4 null

    4 rows selected.

</h6>

###### 생성된 Log File 을 조회한다.

<h6>

    $ cat t1.log
    COMPLETED IN IMPORTING TABLE: PUBLIC.T1, TOTAL 4 RECORDS, SUCCEEDED 4 RECORDS [ Start Time: 2018-2-26 18:39:2 End Time: 2018-2-26 18:39:2 Taken Time: 14879 micro-sec ]

</h6>

###### 생성된 Bad File 을 조회한다.

<h6>

    $ cat t1.bad

</h6>
