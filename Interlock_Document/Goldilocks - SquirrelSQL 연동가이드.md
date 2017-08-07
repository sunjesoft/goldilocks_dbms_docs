### Goldilocks SquirrelSQL 연동 가이드

#### 1. 개요

1 - 1. GOLDILOCKS JDBC DRIVER 를 이용하여 SquirrelSQL 과 연동하는 방법을 설명한다.<br/>
1 - 2. 이 문서는 http://www.squirrelsql.org/ 에서 제공하는 툴을 기준으로 설명한다.<br/>


이 문서의 테스트 환경은 다음과 같다.

    SERVER OS   : LINUX 3.10.0-327.el7.x86_64
    DATABASE    : GOLDILOCKS 3.1.0 r22655
    JDBC DRIVER : goldilocks6.jar

    CLIENT OS   : WINDOWS 7
    JAVA        : 1.8.0_131
    SquirrelSQL : 3.7.1



#### 2. SquirrelSQL 다운로드

2 - 1. http://www.squirrelsql.org/ 사이트에 접속한다.<br/>
2 - 2. 화면 좌측상단 Menu 의 Download and Installation 을 클릭한다.<br/>
2 - 3. Plain zips the latest release for Windows/Linux/MacOS X/others 를 클릭한다.<br/>
2 - 4. squirrelsql-3.7.1-optional.zip 을 누른뒤 다운로드를 진행한다.


사이트 UI 는 언제든지 변경 될 수 있다.<br/>


#### 3. SquirrelSQL 설치

3 - 1. 다운로드 받은 zip 파일을 압축해제한다.<br/>
3 - 2. 인코딩을 UTF-8 로 하려는 경우 압축해제된 경로내의 Squirrel-sql.bat 파일의 내용을 수정한다.<br/>
* 내용 가장 하단 start 부분의 줄에 다음의 내용을 추가한다. <br/>
=> -Dfile.encoding=UTF-8 -Dsun.jnu.encoding=UTF-8
<br/>


    start ... "%LOCAL_JAVA%" -Xmx256m -Dfile.encoding=UTF-8 -Dsun.jnu.encoding=UTF-8 -Dsun.awt.nopixfmt=true ...

3 - 3. Squirrel-sql.bat 파일을 실행한다.


#### 4. GOLDILOCKS JDBC DRIVER 등록

4 - 1. 실행된 SquirrelSQL의 좌측 Drivers 탭을 선택한다.<br/>
4 - 2. + 모양의 Create a New Driver 를 선택한다.<br/>
4 - 3. Extra Class Path 에서 Add 버튼을 선택한 뒤, goldilocks6.jar 파일을 등록한다.<br/>
4 - 4. Name 에 원하는 명(문서에서는 GOLDILOCKS)을 입력한다.<br/>
4 - 5. Example URL 에 GOLDILOCKS JDBC URL 을 입력한다.<br/>
* GOLDILCOKS JDBC URL 은 다음과 같이 구성된다. DSN 은 아무 문자열이나 입력하면 되나 null문자는 허용하지 않는다.<br/>
 => jdbc:goldilocks://Server IP:Listener Port/DSN<br/>
 => 예) SERVER IP 가 192.168.0.50 이고 Listener Port 가 22581 인 경우 URL 구성<br/>
   jdbc:goldilocks://192.168.0.50:22581/goldilocks

4 - 6. Class Name 에 GOLDILCOSK JDBC DRIVER NAME 을 입력한다.<br/>
* GOLDILOCKS JDBC DRIVER NAME 은 다음과 같이 구성된다.<br/>
 => sunje.goldilocks.jdbc.GoldilocksDriver

4 - 7. OK 버튼을 클릭하여 Driver 를 등록한다.

#### 5. GOLDILOCKS Connection 등록

5 - 1. SquirrelSQL의 좌측 Alias 탭을 선택한다.<br/>
5 - 2. + 모양의 Create a New Alias 를 선택한다.<br/>
5 - 3. Name 에 원하는 명(문서에서는 GOLDILOCKS)을 입력한다.<br/>
5 - 4. Driver에 Drivers란의 Name에서 입력한 명(문서에서는 GOLDILOCKS)을 선택한다.<br/>
5 - 5. URL이 Drivers의 Example URL의 내용으로 자동으로 입력되나, 수정할 부분이 있다면 수정한다.<br/>
5 - 6. UserName에 Database 사용자 아이디를 입력한다.<br/>
5 - 7. Password에 Database 사용자 비밀번호를 입력한다.<br/>
5 - 8. Test 버튼을 클릭한 뒤, Connect 버튼을 눌러 연동이 되는지 확인한다.<br/>
* 접속이 성공한 경우 Connection successful 팝업창이 뜬다.<br/>
* 접속이 실패한 경우 URL정보, 방화벽 등을 확인한다.<br/>

5 - 9. OK 버튼을 눌러 Database 에 접속한다.
