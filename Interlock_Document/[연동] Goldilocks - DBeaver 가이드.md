# Goldilocks - DBeaver 연동 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS JDBC DRIVER 를 이용하여 DBeaver 과 연동하는 방법을 설명한다.

#### 1 - 2. 이 문서는 https://dbeaver.jkiss.org/ 에서 제공하는 툴을 기준으로 설명한다.

###### [ 테스트 환경 ]

<h6>

    OS Server   : 3.10.0-693.2.2.el7.x86_64
    DATABASE    : GOLDILOCKS 3.1.4
    JDBC DRIVER : goldilocks6.jar

    CLIENT OS       : WINDOWS 7
    JAVA            : 1.8.0_131
    DBeaver         : 4.3.3.1

</h6>

## 2. DBeaver 다운로드

#### 2 - 1. https://dbeaver.jkiss.org/ 사이트에 접속한다.

#### 2 - 2. Download 를 클릭한다.

#### 2 - 3. Windows 32 bit (installer + JRE) 를 클릭하여 다운로드를 진행한다.

![dbeaver_01](https://user-images.githubusercontent.com/9734988/35428839-938eb01e-02b4-11e8-9286-e59593b1d182.jpg)


## 3. DBeaver 설치

#### 3 - 1. 다운로드 받은 파일을 설치한다.

![dbeaver_02](https://user-images.githubusercontent.com/9734988/35428843-979cc998-02b4-11e8-813a-68cd06c984f9.jpg)

#### 3 - 2. dbeaver 파일을 실행한다.


## 4. GOLDILOCKS ODBC DRIVER 등록

#### 4 - 1. 실행된 DBeaver의 좌측상단 New Connection Wizard 를 선택한다.

#### 4 - 2. ODBC 를 선택한 뒤, Next 를 클릭른다.

![dbeaver_03](https://user-images.githubusercontent.com/9734988/35428849-9e3ea26c-02b4-11e8-9a08-d4505856536e.jpg)

#### 4 - 3. Edit Driver Settings 를 클릭한다.

#### 4 - 4. Edit Driver "ODBC" 란에 값을 기입한다.

###### [ 기입 값 ]

<h6>

    Driver Name  : GOLDILOCKS 를 입력한다.
    Class Name   : GOLDILOCKS JDBC DRIVER NAME 을 입력한다.
                   GOLDILOCKS JDBC DRIVER NAME 은 sunje.goldilocks.jdbc.GoldilocksDriver 이다.
    URL Template : GOLDILOCKS JDBC URL 을 입력한다.
                   GOLDILOCKS JDBC URL 은 jdbc:goldilocks://Server IP:Listener Port/DBName 로 구성된다.
                     DBName 은 아무 문자열이나 입력하면 되나 null 은 허용하지 않는다.
                     예) SERVER IP 가 192.168.0.50 이고 Listener Port 가 22581 인 경우 URL 구성
                      jdbc:goldilocks://192.168.0.50:22581/test
    Default Port : Listener Port 를 입력한다.

    ADD Folder 를 누른 뒤 goldilocks6.jar 파일을 등록한다.
    Find Class 를 누른 뒤 sunje.goldilocks.jdbc.GoldilocksDriver 를 선택한다.

</h6>

![dbeaver_04](https://user-images.githubusercontent.com/9734988/35428856-a1ff098c-02b4-11e8-8fcf-382b3dac3af0.jpg)

<h6>

    Download/Update 버튼을 누른 뒤, Force Download / overwrite 에 체크 한 후, Download 를 진행한다.

</h6>

![dbeaver_05](https://user-images.githubusercontent.com/9734988/35428860-a5218dec-02b4-11e8-8d92-172efdd902b5.jpg)


#### 4 - 5. Test Connection 버튼을 클릭하여 접속이 성공하는지 확인한다.

###### 버그성으로 JDBC URl 이 자동으로 잡히지 않는 경우 Test Connection 버튼을 클릭하면 잡아준다.

###### [ 기입 값 ]

<h6>

    User name : Database 계정 아이디를 입력한다.
    Password  : Database 계정 비밀번호를 입력한다.

</h6>

![dbeaver_06](https://user-images.githubusercontent.com/9734988/35428866-a869d982-02b4-11e8-8bba-6f362dbfd3c9.jpg)

#### 4 - 6. 성공하는 경우 다음단계를 진행하여 Finish 까지 진행한다.

![dbeaver_07](https://user-images.githubusercontent.com/9734988/35428868-ab2050d4-02b4-11e8-84d8-22f0fd9e9311.jpg)


## 6. GOLDILOCKS 쿼리 수행

#### 6 - 1. 쿼리를 입력한 뒤 수행하여 결과를 받아온다.

![dbeaver_08](https://user-images.githubusercontent.com/9734988/35428870-b048f69c-02b4-11e8-9b11-c4af58dbda60.jpg)
