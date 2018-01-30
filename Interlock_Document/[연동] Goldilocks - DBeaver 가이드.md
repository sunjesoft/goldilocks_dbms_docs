# Goldilocks - DBeaver 연동 가이드

## 1. 개요

#### 1 - 1. GOLDILOCKS ODBC DRIVER 를 이용하여 DBeaver 과 연동하는 방법을 설명한다.

#### 1 - 2. 이 문서는 https://dbeaver.jkiss.org/ 에서 제공하는 툴을 기준으로 설명한다.

#### 1 - 3. Goldilocks Windows Client 가 설치되지 않은 경우, 다음 url 을 참고하여 설치를 진행한다.<br/>
https://github.com/sunjesoft/goldilocks_dbms_docs/blob/master/Interlock_Document/%5B%EC%84%A4%EC%B9%98%5D%20Goldilocks%20Windows%20Client%20%EA%B0%80%EC%9D%B4%EB%93%9C.md

###### [ 테스트 환경 ]

<h6>

    OS Server   : 3.10.0-693.2.2.el7.x86_64
    DATABASE    : GOLDILOCKS 3.1.4
    JDBC DRIVER : goldilocks6.jar

    CLIENT OS       : WINDOWS 7
    CLIENT DATABASE : GOLDILOCKS 3.1.4 win32
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

#### 4 - 3. General 에 값을 기입 후, Test Connection 을 클릭한다.

###### [ 기입 값 ]

<h6>

    Database/Schema : odbc 관리자에 등록된 GOLDILOCKS 명을 입력한다.
    User name       : 데이터베이스 계정 아이디를 입력한다.
    Password        : 데이터베이스 계정 비밀번호를 입력한다.

</h6>

![dbeaver_04](https://user-images.githubusercontent.com/9734988/35545407-558a423c-05b2-11e8-990e-390c54fdc9c4.jpg)

#### 4 - 4. Driver settings 창에서 Download 를 클릭하여 필요한 파일을 다운로드한다. 다운로드 후 연결에 성공하면 Connected 라는 팝업창이 뜬다.

![dbeaver_05](https://user-images.githubusercontent.com/9734988/35545412-58333980-05b2-11e8-8e54-20073424aec9.jpg)

#### 4 - 5. Next 와 Finish 를 눌러 연동을 진행한다.

![dbeaver_06](https://user-images.githubusercontent.com/9734988/35545414-59d0799c-05b2-11e8-984e-d8c34d88a389.jpg)

#### 4 - 6. 쿼리를 입력 및 수행하여 데이터를 조회한다.

![dbeaver_07](https://user-images.githubusercontent.com/9734988/35545585-1ed977a2-05b3-11e8-80c9-c7daf6bb31a7.jpg)
