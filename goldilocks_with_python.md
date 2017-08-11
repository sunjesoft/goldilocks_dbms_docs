### Golilocks Python 연동 가이드

#### 개요


python 을 이용하여 Goldilocks 와 연동과정을 설명한다.

1. python 에서 Goldilocks 에 연동하기위해 pyodbc 모듈을 사용한다.
1. 해당 모듈을 사용하기 위해서는 python 사용자의 환경에 unixODBC가 설치되어 있어야 한다.
1. unixODBC 와 Golilocks 연동을 위해 odbcinst.ini 및 .odbc.ini 설정은 필요조건이 아니다.
1. pyodbc 에서는 Driver 를 이용하여 직접연결하는 방식을 사용하기 때문에 unixODBC 설치만으로도 가능하다.

연동을 위해 사용되는 'python' 및 'unixODBC' 의 설치에 대해서는 각 OS 의 패키지 관리자 이용 혹은 소스설치를 진행한다.

##### 준비사항

* python
  * python2 or python3
  * pip or pip3
  * pyodbc
* unixODBC
* Goldilocks Client library

#### python

* python 설치

python 은 <www.python.org> 에서 소스를 받아설치 하거나 yum 및 apt 등의 패키지 관리자를 이용하여 설치 한다.
```
$ python3.6
Python 3.6.1 (default, Apr  7 2017, 09:32:32)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-11)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```
* python 버전
```
chlee@aegis:~
$ python --version
Python 2.7.10
chlee@aegis:~
$ python3.6 --version
Python 3.6.1
```

* 다음은 root 계정으로 python 을 설치한 후, pip 를 이용한 pyodbc 설치 (python3 일 경우 pip3)
```
[root@localhost ~]# pip3 install pyodbc
Collecting pyodbc
  Using cached pyodbc-4.0.16.tar.gz
Installing collected packages: pyodbc
  Running setup.py install for pyodbc ... done
Successfully installed pyodbc-4.0.16
[root@localhost ~]#
```

> 주의할 점은 python 설치계정과 pip 실행계정이 같아서 정상적인 설치가 가능하다.

#### unixODBC

다음은 root 계정을 이용하여 <www.unixODBC.org> 에서 소스를 받거나 yum 및 apt 같은 패키지 관리자를 사용하여 설치한다.
```
[root@localhost ~]# odbcinst -j
unixODBC 2.3.4
DRIVERS............: /usr/local/etc/odbcinst.ini
SYSTEM DATA SOURCES: /usr/local/etc/odbc.ini
FILE DATA SOURCES..: /usr/local/etc/ODBCDataSources
USER DATA SOURCES..: /root/.odbc.ini
SQLULEN Size.......: 8
SQLLEN Size........: 8
SQLSETPOSIROW Size.: 8
[root@localhost ~]#
```
> 설치후 주의점은 pyodbc 에서 unixODBC 를 사용하기 위해 libodbc.so.2 를 사용하게 되므로
python 사용자가 해당 라이브러리 참조할 수 있도록 LD_LIBRARY_PATH 등에 등록을 해야 한다.

#### pyodbc
다음은 pyodbc 를 이용하여 Goldilocks 에 연동하는 예제소스이다.
각 함수에 대한 자세한 사용법은 <https://github.com/mkleehammer/pyodbc/wiki> 을 참조 한다.

```python
import pyodbc

# connection key,value

host   = "HOST=127.0.0.1"
port   = "PORT=22581"
uid    = "UID=test"
pwd    = "PWD=test"

# absolute path => "/home/chlee/workspace/sundb/product/Gliese/home/lib/"
# library name  => "libgoldilockscs-ul64.so"
driver = "DRIVER=/home/chlee/workspace/sundb/product/Gliese/home/lib/libgoldilockscs-ul64.so"
opt1   = "ENABLE_SQLBINDPARAMETER_CONSISTENCY_CHECK=1"


# goldilocks 접속
try:
    connStr = host +";" + port + ";" + uid + ";" + pwd + ";" + driver + ";" + opt1
    conn = pyodbc.connect( connStr , autocommit = True )
    print("goldilocks connected.")

    # encoding 설정, utf-16 을 지원하지 않음
    conn.setencoding(encoding='utf-8', ctype=pyodbc.SQL_CHAR)

    # cursor 생성
    cursor = conn.cursor()

    cursor.execute( "DROP TABLE IF EXISTS T1" )
    print("drop table.")

    cursor.execute( "CREATE TABLE T1( I1 NUMBER , I2 TIMESTAMP )" )
    print("create table.")

    row=( 1, '2017-01-01 12:34:56' )

    cursor.execute( "INSERT INTO T1 VALUES( ?, ?)" , row )
    print("insert data. (%d,%s)" % ( row[0],row[1] ) )

    cursor.execute( "SELECT * FROM T1" )
    print("select t1 data." )
    row = cursor.fetchone()
    if row:
        print( "  i1 = %d i2 = %s " % (row[0],row[1])  )
except Exception as e:
    print( "error msg=[%s]" % str(e) )
finally:
    cursor.close()
    conn.close()
```

> 상기 예제소스는 python3.6 을 기준으로 작성되었으며 python2 에서의 사용법은 python 메뉴얼을 참조한다.
