# Goldilocks - Ruby 연동 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS ODBC, JDBC Driver 를 이용하여 Ruby 와 연동하는 방법을 설명한다.

#### 1 - 2. JDBC 연동 시 JRuby 를 사용한다.

#### 1 - 3. 설치 시, 환경이 맞지 않아 에러가 발생하는 경우 사용자가 설치해야한다.


## 2. Ruby ODBC 를 이용하여 Goldilocks 접속

###### [ 참조 ] JDBC 연동는 '3. Ruby JDBC 를 이용하여 Goldilocks 접속' 을 참조한다.

###### [ 테스트 환경 ]

###### 1. centos 계정에 ruby 를 설치하며, 계정은 unixODBC 를 통해 Goldilocks 에 접속할 수 있어야 한다.
###### unixODBC 연동은 아래 링크를 참조한다. <br/> https://github.com/sunjesoft/goldilocks_dbms_docs/blob/master/Interlock_Document/%5B%EC%97%B0%EB%8F%99%5D%20Goldilocks%20-%20unixODBC%20%EA%B0%80%EC%9D%B4%EB%93%9C.md

###### 2. 설치 시, 옵션으로 설치 경로만 지정한다. 다른 옵션에 대해서는 사용자가 환경에 맞게 설정한다.

<h6>

    OS Server   : CentOS Linux release 7.4.1708
    DATABASE    : Goldilocks 3.1.1 r23756
    gcc         : gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-16)
    unixODBC    : unixODBC 2.3.4

    ruby        : ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-linux]
    rubyODBC    : ruby-odbc-0.99998

</h6>

#### 2 - 1. Ruby 다운로드 및 설치

###### [ 설치 정보 ]

<h6>

    Ruby HOME PATH   : /home/centos/ruby

</h6>

###### 1. wget 명령어를 사용하여 다운로드 한다.

<h6>

    $ whoami
    centos

    $ wget https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.0.tar.gz
    --2018-01-03 13:16:16--  https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.0.tar.gz
    Resolving cache.ruby-lang.org (cache.ruby-lang.org)... 151.101.229.178, 2a04:4e42:36::434
    Connecting to cache.ruby-lang.org (cache.ruby-lang.org)|151.101.229.178|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 15834941 (15M) [application/octet-stream]
    Saving to: ‘ruby-2.5.0.tar.gz’

    100%[=================================================>] 15,834,941  4.60MB/s   in 3.8s

    2018-01-03 13:16:21 (3.97 MB/s) - ‘ruby-2.5.0.tar.gz’ saved [15834941/15834941]

</h6>

###### 2. tar.gz 압축파일을 해제한다.

###### 3. 압축해제한 경로로 들어간다.

###### 4. --prefix 옵션을 사용하여 설치 경로를 지정한다.

<h6>

    $ tar xzf ruby-2.5.0.tar.gz

    $ cd ruby-2.5.0

    $ ./configure --prefix=/home/centos/ruby

    $ make

    $ make install

</h6>

###### 5. Ruby 환경변수를 적용한다.

<h6>

    $ export RUBY_HOME=/home/centos/ruby

    $ export PATH=$RUBY_HOME/bin:$PATH

    $ export LD_LIBRARY_PATH=$RUBY_HOME/lib:$LD_LIBRARY_PATH

</h6>

###### 6. 설치된 Ruby 버전을 확인한다.

<h6>

    $ ruby --version
    ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-linux]

</h6>

#### 2 - 2. RubyODBC 다운로드 및 설치

###### [ 설치 정보 ]

<h6>

    unixODBC HOME PATH   : /home/centos/unixODBC

</h6>

###### 1. wget 명령어를 사용하여 다운로드 한다.

<h6>

    $ wget http://www.ch-werner.de/rubyodbc/ruby-odbc-0.99998.tar.gz
    --2018-01-03 13:41:09--  http://www.ch-werner.de/rubyodbc/ruby-odbc-0.99998.tar.gz
    Resolving www.ch-werner.de (www.ch-werner.de)... 80.150.6.143, 2003:2:2:15:80:150:6:143
    Connecting to www.ch-werner.de (www.ch-werner.de)|80.150.6.143|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 76810 (75K) [application/gzip]
    Saving to: ‘ruby-odbc-0.99998.tar.gz’

    100%[===========================================================>] 76,810      55.3KB/s   in 1.4s

    2018-01-03 13:41:11 (55.3 KB/s) - ‘ruby-odbc-0.99998.tar.gz’ saved [76810/76810]

</h6>

###### 2. tar.gz 압축파일을 해제한다.

###### 3. 압축해제한 경로로 들어간다.

###### 4. ext 경로로 들어간다.

###### 5. --with-odbc-dir 옵션을 사용하며, 옵션 값으로 unixODBC HOME PATH 를 작성한다.


<h6>

    $ tar xzf ruby-odbc-0.99998.tar.gz

    $ cd ruby-odbc-0.99998/ext

    $ ruby extconf.rb --with-odbc-dir=/home/centos/unixODBC

    $ make

    $ make install

</h6>

#### 2 - 3. RubyODBC 를 사용하여 Goldilocks 접속

###### 1. odbc.ini 에 등록된 DSN 을 이용하여 Goldilocks 에 접속한다.

<h6>

    $ cat /home/centos/.odbc.ini
    [ODBC Data Sources]
    GoldilocksODBC = Goldilocks ODBC Driver

    [GoldilocksODBC]
    DRIVER = /home/centos/goldilocks_home/lib/libgoldilockscs-ul64.so
    HOST=192.168.0.50
    PORT=22581

</h6>


###### 2. 테스트 소스를 작성한 뒤 수행한다.

<h6>

    $ cat rubyTest.rb
      require 'odbc'
      $c = ODBC.connect("GoldilocksODBC", "TEST", "test")

      $c.do("DROP TABLE IF EXISTS RUBYTEST")
      $c.run("CREATE TABLE RUBYTEST (ID NUMBER NOT NULL, STR VARCHAR(32) NOT NULL)")

      $q = $c.run("INSERT INTO RUBYTEST (ID, STR) VALUES (1, 'Goldilocks')")

      $q = $c.prepare("SELECT ID, STR FROM RUBYTEST ORDER BY ID")

      $q.execute
      print $q.fetch
      print "\n"
      $q.close

      $c.do("DROP TABLE RUBYTEST")
      $c.disconnect

    $ ruby rubyTest.rb
    [1.0, "Goldilocks"]

</h6>


## 3. Ruby JDBC 를 이용하여 Goldilocks 접속

###### [ 테스트 환경 ]

###### 1. centos 계정에 jruby 를 설치하며, 계정은 Goldilocks 에 접속할 수 있어야 한다.

<h6>

    OS Server   : CentOS Linux release 7.4.1708
    DATABASE    : Goldilocks 3.1.1 r23756
    Java        : 1.8.0_144

    jruby       : jruby 9.1.15.0 (2.3.3)

</h6>


#### 3 - 1. JRuby 다운로드 및 설치

###### [ 설치 정보 ]

<h6>

    JRuby HOME PATH   : /home/centos/jruby-9.1.15.0

</h6>

###### 1. wget 명령어를 사용하여 다운로드 한다.

<h6>

    $ whoami
    centos

    $ wget https://repo1.maven.org/maven2/org/jruby/jruby-dist/9.1.15.0/jruby-dist-9.1.15.0-bin.tar.gz
    --2018-01-03 14:59:27--  https://repo1.maven.org/maven2/org/jruby/jruby-dist/9.1.15.0/jruby-dist-9.1.15.0-bin.tar.gz
    Resolving repo1.maven.org (repo1.maven.org)... 151.101.40.209
    Connecting to repo1.maven.org (repo1.maven.org)|151.101.40.209|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 21043786 (20M) [application/x-gzip]
    Saving to: ‘jruby-dist-9.1.15.0-bin.tar.gz.1’

    100%[========================================================>] 21,043,786   254KB/s   in 68s

    2018-01-03 15:00:35 (304 KB/s) - ‘jruby-dist-9.1.15.0-bin.tar.gz.1’ saved [21043786/21043786]

</h6>

###### 2. tar.gz 압축파일을 해제한다.

###### 3. 압축해제한 경로로 들어간다.

###### 4. bin 경로로 들어간다.

<h6>

    $ tar xzf jruby-dist-9.1.15.0-bin.tar.gz

    $ cd jruby-9.1.15.0/bin

</h6>

###### 5. JRuby 환경변수를 적용한다.

<h6>

    $ export JRUBY_HOME=/home/centos/jruby-9.1.15.0

    $ export PATH=$JRUBY_HOME/bin:$PATH

    $ export LD_LIBRARY_PATH=$JRUBY_HOME/lib:$LD_LIBRARY_PATH

</h6>

###### 6. 설치된 JRuby 버전을 확인한다.

<h6>

    $ jruby --version
    jruby 9.1.15.0 (2.3.3) 2017-12-07 929fde8 OpenJDK 64-Bit Server VM 25.144-b01 on 1.8.0_144-b01 +jit [linux-x86_64]

</h6>

#### 3 - 2. RubyJDBC 를 사용하여 Goldilocks 접속

###### 1. goldilocks6.jar 파일을 $JRUBY_HOME/lib 경로에 복사한다.

<h6>

    $ cp $GOLDILOCKS_HOME/lib/goldilocks6.jar $JRUBY_HOME/lib/

</h6>

###### 2. 테스트 소스를 작성한 뒤 수행한다.

###### [ Goldilocks JDBC 정보 ]

<h6>

    Class Name    : Goldilocks JDBc Driver Name 을 입력한다.
                    Goldilocks JDBc Driver Name 은 sunje.goldilocks.jdbc.GoldilocksDriver 이다.
    url           : Goldilocks JDBC Url 을 입력한다.
                    GOLDILOCKS JDBC URL 은 jdbc:goldilocks://Server IP:Listener Port/DBName 로 구성된다.
                      DBName 은 아무 문자열이나 입력하면 되나 null 은 허용하지 않는다.
                      예) SERVER IP 가 192.168.0.50 이고 Listener Port 가 22581 인 경우 URL 구성
                        jdbc:goldilocks://192.168.0.50:22581/goldilocks

</h6>

<h6>

    $ cat jrubyTest.rb
      driver = Java::JavaClass.for_name("sunje.goldilocks.jdbc.GoldilocksDriver");
      url = "jdbc:goldilocks://192.168.0.50:22581/test"

      con = java.sql.DriverManager.getConnection(url, "TEST", "test");

      stmt = con.prepare_statement('DROP TABLE IF EXISTS JRUBYTEST');
      stmt.executeUpdate();

      stmt = con.prepare_statement('CREATE TABLE JRUBYTEST ( C1 NUMBER, C2 VARCHAR(32))');
      stmt.executeUpdate();

      stmt = con.prepare_statement('INSERT INTO JRUBYTEST (C1, C2) VALUES (1, \'Goldilocks\')');
      stmt.executeUpdate();

      stmt = con.prepare_statement('SELECT C1, C2 FROM JRUBYTEST');
      rowset = stmt.executeQuery()
        while (rowset.next()) do
        puts rowset.getInt(1)
        puts rowset.getString(2)
        end

      stmt = con.prepare_statement('DROP TABLE IF EXISTS JRUBYTEST');
      stmt.executeUpdate();

      con.close();

    $ jruby jrubyTest.rb
    1
    Goldilocks

</h6>
