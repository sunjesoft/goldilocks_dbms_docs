# Goldilocks Windows Client 설치 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS 를 WINDOWS 환경에서 사용할 수 있는 방법을 설명한다.

#### 1 - 2. 연동을 위한 환경 구축은 사용자가 해야 할 사항이므로, 선재소프트는 그 부분에 대한 기술지원을 진행하지 않는다.


###### [ 테스트 환경 ]

<h6>

    OS Server               : CentOS Linux release 7.2.1511
    DATABASE Server Version : GOLDILOCKS 3.1.0 r23084
    DATABASE Server IP      : 192.168.0.50
    DATABASE Server Listener Port : 22581

    OS Client       : WINDOWS 7
    DATABASE Client : GOLDILOCKS 3.1.0 r23084 win32

</h6>

## 2. Goldilocks Windows Client Package 다운로드

#### 2 - 1. Goldilocks Windows Client Package 를 다운로드 한다.<br/>
###### Package 가 없는 경우 technet@sunjesoft.com 으로 요청한다.

#### 2 - 2. OS bit 가 아닌 연동하려는 프로그램 bit 에 맞춰 다운로드를 진행한다.

###### [ 파일 형식 ]

<h6>

    goldilocks-client-<VERSION>-<BIT>.exe

</h6>

## 3. Goldilocks Windows Client Package 설치

#### 3 - 1. exe 파일을 실행한다.

#### 3 - 2. 설치시 원하는 경로를 지정한다.

#### 3 - 3. 설치 버튼을 눌러 설치를 완료한다.

![windows_01](https://user-images.githubusercontent.com/9734988/33203716-5da07cae-d144-11e7-85e1-20fa07f5d9cd.jpg)

## 4. 설치 폴더 구조

#### 4 - 1. 설치 완료 후 goldilocks_home 의 폴더 구조는 다음과 같다.

###### [ 폴더 구조 ]

<h6>

    goldilocks_home
    └ bin                        -- 실행파일
      └ gloadernet.exe
      └ gloctl.exe
      └ gpec.exe
      └ gsqlnet.exe
      └ README
    └ include                    -- 헤더파일
      └ goldilocks.h
      └ goldilocksesql.h
      └ goldilockstypes.h
      └ goldilocksxa.h
      └ sqlca.h
      └ README
    └ lib                        -- 라이브러리파일 (ODBC, JDBC 드라이버)
      └ goldilocks6.jar
      └ goldilocksc.lib
      └ goldilockscs.dll ( 32 bit 설치시 )
      └ goldilockscs-ul64.dll ( 64 bit 설치시 )
      └ goldilockscvtGB18030_32.dll ( 32 bit 설치시 )
      └ goldilockscvtGB18030_64.dll ( 64 bit 설치시 )
      └ goldilockscvtUHC_32.dll ( 32 bit 설치시 )
      └ goldilockscvtUHC_64.dll ( 64 bit 설치시 )
      └ goldilocksesql.lib
      └ goldilockssetup32.dll ( 32 bit 설치시 )
      └ goldilockssetup64.dll ( 64 bit 설치시 )
      └ README
    └ msg
      └ goldilocks_error.msg
      └ README
    └ sample ( 이하 내용 생략 ) -- 샘플파일
      └ Backup
      └ EmbeddedSQL
      └ JDBC
      └ ODBC
      └ SQL

</h6>

## 5. Goldilocks Windows ODBC Driver 등록

#### 5 - 1. ODBC Driver 등록을 위해 다음의 경로에 들어간다.

<h6>

    32 bit OS 에서 설치를 32 bit로 한 경우 : C:\Windows\System32
    64 bit OS 에서 설치를 64 bit로 한 경우 : C:\Windows\System32
    64 bit OS 에서 설치를 32 bit로 한 경우 : C:\Windows\SysWOW64

</h6>

#### 5 - 2. 경로에 들어간 후, odbcad32.exe 프로그램을 실행한다.

#### 5 - 3. 사용자 DSN 탭에서 추가버튼을 선택한다.

#### 5 - 4. GOLDILOCKS 드라이버를 선택한 후 마침을 클릭한다.

<h6>

    설치를 64 bit로 한 경우 드라이버 이름은 GOLDILOCKS_64 이다.
    설치를 32 bit로 한 경우 드라이버 이름은 GOLDILOCKS_32 이다.

</h6>

![windows_02](https://user-images.githubusercontent.com/9734988/33203841-c818802c-d144-11e7-9b6f-eedd37beb7ac.jpg)

#### 5 - 5. GOLDILOCKS Driver Configuration 에 값을 입력한다.<br/>
###### 옵션에 대해서는 상황에 맞게 입력 및 선택하며, 하단에서 설명한다.

#### 5 - 6. OK 버튼을 눌러 Goldilocks Windows ODBC Driver 를 등록한다.

![windows_03](https://user-images.githubusercontent.com/9734988/33203850-d02efc82-d144-11e7-87d5-e604688fd7df.jpg)

<h6>

| 옵션 | 값 | 설명 |
|:--  |:-- |:-- |
|*(필수) DSN         ||데이터 원본 이름을 입력한다.<br/>(문서에서는 Goldilocks 로 작성한다.)|
|*(필수) HOST        ||Database Server IP 주소를 입력한다. <br/>예) 192.168.0.50|
|*(필수) PORT        ||Listener Port 를 입력한다. <br/>예) 22581|
|UID         ||Database 사용자 아이디를 입력한다.|
|CS Mode     |Default|Listener 속성에 따라 모드가 결정된다.|
|            |Dedicated|Dedicated 모드로 접속한다.|
|            |Shared|Shared 모드로 접속한다. 서버에서 SHARED SESSION 모드가 설정되어 있어야 한다.|
|FAILOVER|Connection Retry<br/>(Count)|연결 실패 시, 서버 접속 시도 횟수를 입력한다.|
|        |Connection Retry<br/>(Delay)|연결 실패 시, 서버 접속 시도 간격(단위 : 초)를 입력한다.|
|        |Type(Connection)|연결 실패 시, AlternateServers 로 연결한다.|
|        |Type(Session)   |연결 실패 및 Statement 동작 중 연결이 끊어진 경우, AlternateServers 로 연결 후 Statement 를 복원하여 수행한다.|
|        |Granularity<br/>(Non-atomic)|Failover 진행 중, 에러가 발생해도 Failover 를 진행한다.|
|        |Granularity<br/>(Atomic) | Failover 진행 중, 에러가 발생하는 경우 Failover 를 중단하고 실패로 처리한다.|
|        |AlternateServers| 연결 실패 시, 연결을 시도하는 서버 리스트(다중일 경우 , 로 구분)를 입력한다. <br/>예) HOST=192.168.0.51:PORT=22581,HOST=...|
|Format  |DATE|날짜 타입 형식 문자열을 설정한다.|
|        |TIME|시간 타입 형식 문자열을 설정한다.|
|        |TIME WITH TIME ZONE|시간 + 타임존 타입 형식 문자열을 설정한다.|
|        |TIMESTAMP|타임스탬프 형식 문자열을 설정한다.|
|        |TIMESTAMP WITH TIME ZONE|타임스탬프 + 타임존 타입 형식 문자열을 설정한다.|
|Character Length Unit|Octets|컬럼 사이즈를 바이트 단위로 지정한다.|
|       |Characters|컬럼 사이즈를 문자 단위로 지정한다.|

</h6>

#### 6. Goldilocks 접속 응용 프로그램(gsqlnet)을 이용하여 접속테스트

###### 6 - 1. cmd 창을 실행한다.

###### 6 - 2. 입력한 DSN 명을 사용하여, gsqlnet 명령어로 데이터베이스에 접속한다.

![windows_04](https://user-images.githubusercontent.com/9734988/33203885-f4c385f4-d144-11e7-91f3-c93647354548.jpg)
