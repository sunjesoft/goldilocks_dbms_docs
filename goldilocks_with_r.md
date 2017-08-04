
## R 연동 방법 

### 개요 

본 문서는 R 과 SUNDB 간의 연동 방법에 대해서 기술한다. R 은 ODBC 와 JDBC 두가지 방식을 지원하는데,  본 문서에서는 ODBC 를 사용한 방법만 기술한다. 

### 전제 

본 문서는 다음과 같은 전제를 가정하고 쓰여졌다. 
* SUNDB 가 이미 설치되어있고, 구동중이며, Listener 또한 구동되어 외부에서 TCP 를 통한 접속이 가능한 상태이다. 
* unixODBC 가 설치되어있다. 만약 설치 안되었다면 UnixODBC 연동 가이드를 참조한다. 
* R 이 설치되어있고, 사용자는 해당 설치 버젼이 32bit 인지 64bit 인지 알고 있다. 


### 연동 방법 

#### 1. DSN 설정 

R 을 사용하고자하는 사용자 계정의 .odbc.ini 파일을 열어서 확인한다. 

```
[SUNDB]
HOST = 127.0.0.1
PORT = 22581
DRIVER = /path/to/your/odbcdriver

```

HOST 와 PORT는 현재 구동되어 있는 SUNDB 의 HOST / PORT 를 기술하면 되고, DRIVER 는 $SUNDB_HOME/lib 에 존재하는 
ODBC Driver 를 지정한다. 보통 64bit R 에서 사용하는 드라이버는 libsundbcs-ul64.so 을 사용하면 된다. 위 파일이 존재하는 위치를 지정한다. 


#### 2. RODBC 설치 

R 를 수행하고 R 에서 제공하는 RODBC 를 설치한다. 

```
> package.install ( "RODBC" )

```

위와 같이했을때 제대로 설치 되면 성공이다. 단 unixODBC Driver 를 System 기본 위치에 설치 하지 않았다면 오류가 발생할 수 있는데, 이때는 다음과 같이 
환경변수를 세팅하여 오류를 회피 할 수 있다. 

```
export ODBC_INCLUDE=/path/to/odbc/include
export ODBC_LIBS=/path/to/odbc/lib
```

R 이 64bit 일 경우는 Server 의 ODBC Driver 를 그대로 사용하면 되고, 32bit 라면 별도의 32bit Client Package 를 요청하여 해당 드라이버를 
DSN 에 등록하여야 한다. 


##### 3. 간단한 예제

다음은 테이블의 데이터를 조회하여 R 에 로딩하는 간단한 예제이다. 

```
library ( RODBC )
con <- odbcConnect(dsn="SUNDB",uid="TEST",pwd="test",DBMSencoding="UTF-8")
res <- sqlQuery ( con, "SELECT 'AAA' AS NAME, 1 AS VALUE FROM DUAL ")
rtn <- odbcClose ( con )
```
