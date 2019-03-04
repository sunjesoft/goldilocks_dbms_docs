# Spring Boot - GOLDILOCKS 연동 가이드(Linux)

## 1. 개요

1-1. 본 문서에서는 Spring boot와 maven, mybatis를 사용한 구축 환경에서 GOLDILOCKS를 연동하여 사용하는 방법을 설명한다.

1-2. 각 프레임워크의 연동, 실행 방법은 환경마다 다를 수 있으며 프레임워크 레벨에서의 문제는 사용자가 해결해야 한다.

- 사용 OS 버전 : CentOS Linux release 7.2.1511 (Core)
- spring 버전에 맞는 jdk 환경이 구성되어 있어야 한다.($JAVA_HOME 환경변수 설정 필요)

## 2. maven 환경 구축

    $ wget http://apache.mirror.cdnetworks.com/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
    $ tar -xvf apache-maven-3.6.0-bin.tar.gz
    $ cd apache-maven-3.6.0
    $ pwd
    /home/spring/package/apache-maven-3.6.0

#### profile 설정

    export M2_HOME=/home/spring/package/apache-maven-3.6.0
    export PATH=$PATH:$M2_HOME/bin

#### 설치 확인

    $ mvn -v
    Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T03:41:47+09:00)
    Maven home: /home/spring/package/apache-maven-3.6.0
    Java version: 1.8.0_181, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
    Default locale: ko_KR, platform encoding: UTF-8
    OS name: "linux", version: "3.10.0-327.el7.x86_64", arch: "amd64", family: "unix"


## 3. spring-boot 예제 생성, 실행

https://start.spring.io/ 에 접속한다.

![spring-example](https://github.com/sunjesoft/goldilocks_dbms_docs/blob/master/Image/Spring%20Initializr.png?raw=true)

Maven Project, java, Spring boot의 버전을 선택하고 Depeandencies의 Web을 선택한다.

Generate Project를 클릭하면 zip형태의 파일로 예제가 제공되며, 이를 리눅스 서버에 이동시킨다.

    $ unzip demo.zip
    Archive:  demo.zip
       creating: demo/
       .....
      inflating: demo/src/main/resources/application.properties
      inflating: demo/src/test/java/com/example/demo/DemoApplicationTests.java

    $ cd demo
    $ mvn spring-boot:run

    [INFO] Scanning for projects...
    [INFO]
    [INFO] --------------------------< com.example:demo >--------------------------
    [INFO] Building demo 0.0.1-SNAPSHOT
    [INFO] --------------------------------[ jar ]---------------------------------
    [INFO]
    [INFO] >>> spring-boot-maven-plugin:2.1.2.RELEASE:run (default-cli) > test-compile @ demo >>>
    .....
    2019-01-23 13:54:34.428  INFO 11934 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 1.898 seconds (JVM running for 4.207)


구동 확인 후 웹브라우저 등을 통해 서버의 8080포트에 접속하면 된다.

다음과 같은 페이지가 보이면 정상적으로 구동된 것이다.

![webrun](https://github.com/sunjesoft/goldilocks_dbms_docs/blob/master/Image/webserver%20run.png?raw=true)


## 4. GOLDILOCKS 연동을 위한 환경 설정

연동을 시작하기 전에, goldilocks6.jar파일이 준비되어 있어야 한다.
GOLDILOCKS JDBC를 사용하기 위한 라이브러리 파일로, 서버 패키지 혹은 클라이언트(windows) 설치시 동봉되어 있다.

서버 패키지 : $GOLDILOCKS_HOME/lib/goldilocks6.jar
클라이언트 패키지(windows) : C:\Program Files (x86)\GOLDILOCKS 3.x.x\goldilocks_home\lib\goldilocks6.jar

    $ pwd
    /home/spring/demo
    $ mkdir lib
    $ mv goldilocks6.jar lib
    $ vi pom.xml
pom.xml을 수정한다.

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

Dependencies에 다음과 같은 항목을 추가해 준다.

    <dependencies>
        <dependency>
            <groupId>GOLDILOCKS</groupId>
            <artifactId>GOLDILOCKS_test</artifactId>
            <version>3.1.20</version>
            <scope>system</scope>
            <systemPath>${base-dir}/lib/goldilocks6.jar</systemPath>
        </dependency>
    </dependencies>

추가 후 디버깅 모드로 실행하면 라이브러리 로딩이 되는지 여부를 확인할 수 있다.

    $ mvn spring-boot:run -X
    Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T03:41:47+09:00)
    Maven home: /home/spring/package/apache-maven-3.6.0
    Java version: 1.8.0_181, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
    Default locale: ko_KR, platform encoding: UTF-8
    OS name: "linux", version: "3.10.0-327.el7.x86_64", arch: "amd64", family: "unix"
    ...
    [DEBUG]   (f) compilePath = [... /home/spring/demo/lib/goldilocks6.jar]

## 5. GOLDILOCKS 연동 샘플

프로젝트 구조는 다음과 같다.

    |-- HELP.md
    |-- lib
    |   `-- goldilocks6.jar
    |-- pom.xml
    |-- src
    |   `-- main
    |       |-- java
    |       |   `-- com
    |       |       `-- example
    |       |           `-- demo
    |       |               |-- DemoApplication.java
    |       |               |-- JspTest.java
    |       |               `-- board
    |       |                   |-- controller
    |       |                   |-- domain
    |       |                   |   `-- BoardVO.java
    |       |                   |-- mapper
    |       |                   |   |-- BoardMapper.java
    |       |                   |   `-- BoardMapper.xml
    |       |                   `-- service
    |       |-- resources
    |       |   |-- application.properties
    |       |   |-- static
    |       |   `-- templates
    |       `-- webapp
    |           `-- test
    |               `-- views
    |                   `-- test.jsp
    `-- target
        |-- classes
        |   |-- META-INF
        |   |   |-- MANIFEST.MF
        |   |   `-- maven
        |   |       `-- com.example
        |   |           `-- demo
        |   |               |-- pom.properties
        |   |               `-- pom.xml
        |   |-- application.properties
        |   `-- com
        |       `-- example
        |           `-- demo
        |               |-- DemoApplication.class
        |               |-- JspTest.class
        |               `-- board
        |                   |-- controller
        |                   |-- domain
        |                   |   `-- BoardVO.class
        |                   |-- mapper
        |                   |   |-- BoardMapper.class
        |                   |   `-- BoardMapper.xml
        |                   `-- service
        |-- generated-sources
        |   `-- annotations
        |-- maven-archiver
        |   `-- pom.properties
        |-- maven-status
        |   `-- maven-compiler-plugin
        |       `-- compile
        |           `-- default-compile
        |               |-- createdFiles.lst
        |               `-- inputFiles.lst
        `-- test-classes

#### DemoApplication.java

    package com.example.demo;

    import javax.sql.DataSource;

    import org.apache.ibatis.session.SqlSessionFactory;
    import org.mybatis.spring.SqlSessionFactoryBean;
    import org.mybatis.spring.annotation.MapperScan;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.Bean;


    @SpringBootApplication
    @MapperScan(value={"com.example.demo.board.mapper"})
    public class DemoApplication {

            public static void main(String[] args) {
                    SpringApplication.run(DemoApplication.class, args);
                    System.out.println("Hello JavaWorld");
            }


                    @Bean
                public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception{

                    SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();

                    sessionFactory.setDataSource(dataSource);
                    return sessionFactory.getObject();

            }

    }

#### JspTest.java

    package com.example.demo;

    import javax.annotation.Resource;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;

    import com.example.demo.board.mapper.BoardMapper;

    @Controller
    public class JspTest {

        @Resource(name="com.example.demo.board.mapper.BoardMapper")
        BoardMapper mBoardMapper;

        @RequestMapping("/test")
        private String jspTest() throws Exception{

            System.out.println(mBoardMapper.boardCount()); // <<<<TEST

            return "test";
        }
    }


#### BoardVO.java

    package com.example.demo.board.domain;

    import java.util.Date;

    public class BoardVO {

        private int bno;
        private String subject;
        private String content;
        private String writer;
        private Date reg_date;
            public int getBno() {
                    return bno;
            }
            public void setBno(int bno) {
                    this.bno = bno;
            }
            public String getSubject() {
                    return subject;
            }
            public void setSubject(String subject) {
                    this.subject = subject;
            }
            public String getContent() {
                    return content;
            }
            public void setContent(String content) {
                    this.content = content;
            }
            public String getWriter() {
                    return writer;
            }
            public void setWriter(String writer) {
                    this.writer = writer;
            }
            public Date getReg_date() {
                    return reg_date;
            }
            public void setReg_date(Date reg_date) {
                    this.reg_date = reg_date;
            }

    }


#### BoardMapper.java

    package com.example.demo.board.mapper;

    import org.springframework.stereotype.Repository;

    @Repository("com.example.demo.board.mapper.BoardMapper")
    public interface BoardMapper {

        public int boardCount() throws Exception;
    }


#### BoardMapper.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

    <mapper namespace="com.example.demo.board.mapper.BoardMapper">

        <select id="boardCount" resultType="int">
            SELECT
                COUNT(*)
            FROM BOARD
        </select>
    </mapper>

COUNT를 반환하는 쿼리기 때문에 BOARD 테이블의 구조는 뭐라도 상관없다.


#### application.properties

    spring.datasource.driverClassName=sunje.goldilocks.jdbc.GoldilocksDriver
    spring.datasource.url=jdbc:goldilocks://127.0.0.1:22581/test
    spring.datasource.username=sys
    spring.datasource.password=gliese

    spring.mvc.view.prefix=/test/views/
    spring.mvc.view.suffix=.jsp


#### test.jsp

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
    </head>
    <body>

    <h2> TEST!!!! </h2>

    </body>
    </html>




#### 구동 테스트

해당 프로젝트에서 mvn spring-boot:run을 실행한다.

    $ mvn spring-boot:run
    .   ____          _            __ _ _
    /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
    \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
    '  |____| .__|_| |_|_| |_\__, | / / / /
    =========|_|==============|___/=/_/_/_/
    :: Spring Boot ::        (v2.1.3.RELEASE)

    2019-03-04 15:29:50.873  INFO 27522 --- [           main] com.example.demo.DemoApplication         : Starting DemoApplication with PID 27522 (/home/spring/project/window/target/classes started by spring in /home/spring/project/window)
    2019-03-04 15:29:50.876  INFO 27522 --- [           main] com.example.demo.DemoApplication         : No active profile set, falling back to default profiles: default
    2019-03-04 15:29:52.074  INFO 27522 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
    2019-03-04 15:29:52.106  INFO 27522 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
    2019-03-04 15:29:52.106  INFO 27522 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.16]
    2019-03-04 15:29:52.117  INFO 27522 --- [           main] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib]
    2019-03-04 15:29:52.243  INFO 27522 --- [           main] org.apache.jasper.servlet.TldScanner     : At least one JAR was scanned for TLDs yet contained no TLDs. Enable debug logging for this logger for a complete list of JARs that were scanned but no TLDs were found in them. Skipping unneeded JARs during scanning can improve startup time and JSP compilation time.
    2019-03-04 15:29:52.247  INFO 27522 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
    2019-03-04 15:29:52.248  INFO 27522 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1212 ms
    2019-03-04 15:29:52.677  INFO 27522 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
    2019-03-04 15:29:52.969  INFO 27522 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
    2019-03-04 15:29:52.975  INFO 27522 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 2.46 seconds (JVM running for 6.027)
    Hello JavaWorld


웹 브라우저 등을 통해서 해당 주소의 /test 경로에 접근하면 콘솔창에서 count 수를 반환하게 된다.

    2019-03-04 15:37:00.281  INFO 27881 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
    2019-03-04 15:37:00.281  INFO 27881 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
    2019-03-04 15:37:00.291  INFO 27881 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 9 ms
    2019-03-04 15:37:13.493  INFO 27881 --- [nio-8080-exec-3] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
    2019-03-04 15:37:13.566  INFO 27881 --- [nio-8080-exec-3] com.zaxxer.hikari.pool.PoolBase          : HikariPool-1 - Driver does not support get/set network timeout for connections. (sunje.goldilocks.jdbc.GoldilocksConnection.getNetworkTimeout()I)
    2019-03-04 15:37:13.605  INFO 27881 --- [nio-8080-exec-3] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
    2 -- table의 count 수


## 6. JAR 빌드

JAR 파일을 빌드해서 웹서버를 구동시키기 원한다면 별도의 작업이 필요하다.
기존 설정대로라면 goldilocks6.jar 파일은 외부 라이브러리로 취급되기 때문에 JAR파일로 생성될 때 포함되지 않게 된다.
maven local repository 등록을 통해 JAR파일 빌드시 포함되게 할 수 있다.

    $ cd lib -- goldilocks6.jar파일의 경로
    $ ls goldilocks6.jar
    goldilocks6.jar
    $ mvn install:install-file -Dfile=goldilocks6.jar -DgroupId=goldilocks -DartifactId=jdbc -Dversion=6 -Dpackaging=jar
    $ cd .. -- project 디렉토리
    $ mvn package
    [INFO]
    [INFO] --------------------------< com.example:demo >--------------------------
    [INFO] Building demo 0.0.1-SNAPSHOT
    [INFO] --------------------------------[ jar ]---------------------------------
    [INFO]
    [INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ demo ---
    [INFO] Using 'UTF-8' encoding to copy filtered resources.
    [INFO] Copying 1 resource
    [INFO] Copying 0 resource
    [INFO]
    [INFO] --- maven-compiler-plugin:3.8.0:compile (default-compile) @ demo ---
    [INFO] Nothing to compile - all classes are up to date
    [INFO]
    [INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ demo ---
    [INFO] Using 'UTF-8' encoding to copy filtered resources.
    [INFO] skip non existing resourceDirectory /home/spring/project/window/src/test/resources
    [INFO]
    [INFO] --- maven-compiler-plugin:3.8.0:testCompile (default-testCompile) @ demo ---
    [INFO] No sources to compile
    [INFO]
    [INFO] --- maven-surefire-plugin:2.22.1:test (default-test) @ demo ---
    [INFO]
    [INFO] --- maven-jar-plugin:3.1.1:jar (default-jar) @ demo ---
    [INFO] Building jar: /home/spring/project/window/target/demo-0.0.1-SNAPSHOT.jar
    [INFO]
    [INFO] --- spring-boot-maven-plugin:2.1.3.RELEASE:repackage (repackage) @ demo ---
    [INFO] Replacing main artifact with repackaged archive
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  1.841 s
    [INFO] Finished at: 2019-03-04T16:20:09+09:00
    [INFO] ------------------------------------------------------------------------

packaging이 완료되면 target 디렉토리에 해당 파일이 생성된다.

    $ ls target/*.jar
    target/demo-0.0.1-SNAPSHOT.jar

jar -tf 명령어로 jar파일의 내용을 확인할 수 있다. grep 등으로 GOLDILOCKS를 찾으면 포함 여부를 확인하기 쉽다.

    $ jar -tf demo-0.0.1-SNAPSHOT.jar
    META-INF/
    META-INF/MANIFEST.MF
    ...
    BOOT-INF/lib/spring-core-5.1.5.RELEASE.jar
    BOOT-INF/lib/spring-jcl-5.1.5.RELEASE.jar
    BOOT-INF/lib/GOLDILOCKS_TEST-3.1.21.jar  -- goldilocks jar 파일이 포함되어 있다.
    BOOT-INF/lib/mybatis-spring-boot-starter-1.1.1.jar
    BOOT-INF/lib/mybatis-spring-boot-autoconfigure-1.1.1.jar
    ...

단, 라이브러리를 포함해서 빌드할 경우 pom.xml의 항목 또한 수정되어야 한다.

    <dependency>
       <groupId>GOLDILOCKS</groupId>
       <artifactId>GOLDILOCKS_TEST</artifactId>
       <version>3.1.21</version>
    </dependency>

scope와 systemPath를 삭제하면 된다.
