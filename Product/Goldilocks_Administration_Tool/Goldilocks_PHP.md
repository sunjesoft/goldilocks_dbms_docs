## Goldilocks PHP 연동 가이드

#### 1. 개요

1. Goldilocks 와 PHP 를 연동하는 방법을 설명한다.
2. 이 문서는 http://php.net/ 에서 제공하는 PHP 를 기준으로 설명한다.
3. 연동을 위한 환경 구축은 사용자가 해야 할 사항이므로, 선재소프트는 그 부분에 대한 기술지원을 진행하지 않는다.
4. PHP 를 연동하기 위해서는 사전에 Goldilocks 와 unixODBC 가 연동되어 있어야 한다.
5. PHP 설치 시 컴파일 수행을 위한 환경을 체크하며, 환경에 맞는 바이너리가 없어 에러가 발생하는 경우 직접 설치해야한다.

#### 2. 설치 환경


    SERVER OS       : CentOS Linux release 7.2.1511
    SERVER DATABASE : Goldilocks 3.1.0 r23226

    CLIENT OS       : CentOS Linux release 7.2.1511
    CLIENT unixODBC : unixODBC v2.3.4
    CLIENT PHP      : PHP v7.1.9

#### 3. PHP 다운로드

[ 다운로드 방법 1 ]

1. http://php.net/ 사이트에 접속한다.
2. Download 탭을 클릭한뒤, tar.gz 압축파일을 클릭한다.
3. Republic of Korea 에서 kr1.php.net 을 클릭하여 다운로드를 진행한다.
4. 다운로드한 파일을 클라이언트 OS 로 전송한다.

<img src="E:\pic\PHP\PHP_01.jpg" alt="PHP_01" style="width : 450px;"/>
<img src="E:\pic\PHP\PHP_02.jpg" alt="PHP_02" style="width : 450px;"/>


[ 다운로드 방법 2 ]

1. 클라이언트 OS 에서 wget 명령어를 사용하여 다운로드 한다.


    $ wget http://kr1.php.net/get/php-7.1.9.tar.gz/from/this/mirror
    --2017-09-13 10:56:34--  http://kr1.php.net/get/php-7.1.9.tar.gz/from/this/mirror
    Resolving kr1.php.net (kr1.php.net)... 115.68.110.81
    Connecting to kr1.php.net (kr1.php.net)|115.68.110.81|:80... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: http://kr1.php.net/distributions/php-7.1.9.tar.gz [following]
    --2017-09-13 10:56:34--  http://kr1.php.net/distributions/php-7.1.9.tar.gz
    Reusing existing connection to kr1.php.net:80.
    HTTP request sent, awaiting response... 200 OK
    Length: 19428408 (19M) [application/x-gzip]
    Saving to: ‘mirror’

    100%[====================================>] 19,428,408  7.22MB/s   in 2.6s

    2017-09-13 10:56:37 (7.22 MB/s) - ‘mirror’ saved [19428408/19428408]

#### 4. PHP 설치 전

[ 설치 정보 ]


    사용자 계정       : centos
    설치 경로         : /home/centos/PHP
    unixODBC 경로     : /home/centos/unixODBC

#### 5. PHP 설치

1. tar.gz 혹은 mirror(wget 사용 시)파일을 압축해제한다.
2. 압축해제한 경로로 들어간다.
3. PHP 를 설치한다. 이 때, 옵션으로 --witn-unixODBC 가 들어가야 한다.


    $ tar xzf mirror

    $ cd php-7.1.9

    $ ./configure --prefix=/home/centos/PHP --with-unixODBC=/home/centos/unixODBC --disable-all

    $ make

    $ make install

#### 6. PHP 연동

1. PHP 가 설치된 경로로 이동한다.
2. bin 폴더로 이동한다.
3. 연동 소스를 작성한 뒤, 소스를 수행한다.


    $ cd /home/centos/PHP

    $ cd bin

    $ cat goldilocks.php
    <?php
    $con=odbc_connect('GoldilocksODBC', 'TEST', 'test');

    if($con){
        echo "Connection Success\n";
        odbc_close($con);
    }else{
        echo "Connection Fail\n";
    }
    ?>


    $ ./php goldilocks.php
    Connection Success
