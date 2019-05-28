{\rtf1\ansi\ansicpg1252\cocoartf1671\cocoasubrtf500
{\fonttbl\f0\froman\fcharset0 Times-Bold;\f1\fnil\fcharset129 AppleMyungjo;\f2\froman\fcharset0 Times-Roman;
}
{\colortbl;\red255\green255\blue255;\red0\green0\blue0;\red0\green0\blue233;}
{\*\expandedcolortbl;;\cssrgb\c0\c0\c0;\cssrgb\c0\c0\c93333;}
\paperw11900\paperh16840\margl1440\margr1440\vieww12600\viewh10200\viewkind0
\deftab720
\pard\pardeftab720\sl280\partightenfactor0

\f0\b\fs24 \cf2 \expnd0\expndtw0\kerning0
# GOLDILOCKS - Jboss(Wildfly) 
\f1\b0 \'bf\'ac\'b5\'bf
\f0\b  
\f1\b0 \'b0\'a1\'c0\'cc\'b5\'e5
\f2 \

\f0\b \
## 
\f1\b0 \'b8\'f1\'c2\'f7
\f2 \

\f0\b \
\
1. [
\f1\b0 \'b0\'b3\'bf\'e4
\f0\b ](#
\f1\b0 \'b0\'b3\'bf\'e4
\f0\b )
\f2\b0 \

\f0\b 2. [Jboss 
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b  
\f1\b0 \'bb\'fd\'bc\'ba
\f0\b ](#jboss-
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b -
\f1\b0 \'bb\'fd\'bc\'ba
\f0\b )
\f2\b0 \

\f0\b 3. [JAVA 
\f1\b0 \'bc\'b3\'c4\'a1
\f0\b ](#java-
\f1\b0 \'bc\'b3\'c4\'a1
\f0\b )
\f2\b0 \

\f0\b 4. [Jboss(7.1.1) 
\f1\b0 \'bc\'b3\'c4\'a1
\f0\b ](#jboss-
\f1\b0 \'bc\'b3\'c4\'a1
\f0\b )
\f2\b0 \

\f0\b 5. [Jboss 
\f1\b0 \'b1\'e2\'b5\'bf
\f0\b ](#jboss-aws-
\f1\b0 \'b1\'e2\'b5\'bf
\f0\b )
\f2\b0 \

\f0\b 6. [goldilocks 
\f1\b0 \'bf\'ac\'b5\'bf
\f0\b ](#goldilocks-
\f1\b0 \'bf\'ac\'b5\'bf
\f0\b )
\f2\b0 \

\f0\b 7. [APP Test](#app-test)
\f2\b0 \

\f0\b 8. [
\f1\b0 \'b9\'e8\'c6\'f7
\f0\b ](#
\f1\b0 \'b9\'e8\'c6\'f7
\f0\b )
\f2\b0 \

\f0\b 9. [Check](#check)
\f2\b0 \

\f0\b 10. [XA DataSource 
\f1\b0 \'bf\'ac\'b0\'e1
\f0\b ](#xa-datasource-
\f1\b0 \'bf\'ac\'b0\'e1
\f0\b )
\f2\b0 \

\f0\b 11. [Wildfly(16.0.0) 
\f1\b0 \'bf\'ac\'b5\'bf\'bd\'c3
\f0\b  
\f1\b0 \'c1\'d6\'c0\'c7\'bb\'e7\'c7\'d7
\f0\b ](#wildfly-
\f1\b0 \'bf\'ac\'b5\'bf\'bd\'c3
\f0\b -
\f1\b0 \'c1\'d6\'c0\'c7\'bb\'e7\'c7\'d7
\f0\b )
\f2\b0 \

\f0\b \
## 
\f1\b0 \'b0\'b3\'bf\'e4
\f2 \

\f0\b * GOLDILOCKS JDBC Driver 
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'c0\'cc\'bf\'eb\'c7\'cf\'bf\'a9
\f0\b  Jboss 
\f1\b0 \'bf\'cd
\f0\b  
\f1\b0 \'bf\'ac\'b5\'bf\'c7\'cf\'b4\'c2
\f0\b  
\f1\b0 \'b9\'e6\'b9\'fd\'c0\'bb
\f0\b  
\f1\b0 \'bc\'b3\'b8\'ed\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b * 
\f1\b0 \'bc\'b3\'c4\'a1
\f0\b  
\f1\b0 \'bd\'c3
\f0\b , 
\f1\b0 \'c8\'af\'b0\'e6\'c0\'cc
\f0\b  
\f1\b0 \'b8\'c2\'c1\'f6
\f0\b  
\f1\b0 \'be\'ca\'be\'c6
\f0\b  
\f1\b0 \'bf\'a1\'b7\'af\'b0\'a1
\f0\b  
\f1\b0 \'b9\'df\'bb\'fd\'c7\'cf\'b4\'c2
\f0\b  
\f1\b0 \'b0\'e6\'bf\'ec
\f0\b  
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da\'b0\'a1
\f0\b  
\f1\b0 \'bc\'b3\'c4\'a1\'c7\'d8\'be\'df\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b \
###### [ 
\f1\b0 \'c5\'d7\'bd\'ba\'c6\'ae
\f0\b  
\f1\b0 \'c8\'af\'b0\'e6
\f0\b  ]
\f2\b0 \

\f0\b \
###### * Jboss 
\f1\b0 \'b0\'e8\'c1\'a4\'bf\'a1
\f0\b  Jboss 
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'bc\'b3\'c4\'a1\'c7\'cf\'b8\'e7
\f0\b , 
\f1\b0 \'b0\'e8\'c1\'a4\'c0\'ba
\f0\b  Goldilocks glsnr(TCP) port 
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'c5\'eb\'c7\'d8
\f0\b  Goldilocks 
\f1\b0 \'bf\'a1
\f0\b  
\f1\b0 \'c1\'a2\'bc\'d3\'c7\'d2
\f0\b  
\f1\b0 \'bc\'f6
\f0\b  
\f1\b0 \'c0\'d6\'be\'ee\'be\'df
\f0\b  
\f1\b0 \'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ###### * Jboss 
\f1\b0 \'b1\'e2\'b5\'bf\'bd\'c3
\f0\b  
\f1\b0 \'bd\'c7\'c7\'e0\'b5\'c7\'b4\'c2
\f0\b  port
\f1\b0 \'bf\'a1
\f0\b  
\f1\b0 \'b4\'eb\'c7\'d1
\f0\b  
\f1\b0 \'b9\'e6\'c8\'ad\'ba\'ae\'c0\'cc
\f0\b  
\f1\b0 \'bf\'ad\'b7\'c1\'c0\'d6\'be\'ee\'be\'df
\f0\b  
\f1\b0 \'c7\'d1\'b4\'d9
\f0\b . (default : 8080,9990,8443..)
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0OS Server\'a0\'a0\'a0: CentOS Linux release 7.6.1810
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0DATABASE\'a0\'a0\'a0\'a0: Goldilocks 3.2.2 r27513
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0JDK\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0: java version "1.7.0_211"
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Jboss\'a0\'a0\'a0\'a0\'a0\'a0\'a0: Jboss AS 7.1.1
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Wildfly\'a0\'a0\'a0\'a0\'a0: wildfly 17.0.0
\f2\b0 \

\f0\b \
</h6>
\f2\b0 \

\f0\b \
## Jboss 
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b  
\f1\b0 \'bb\'fd\'bc\'ba
\f2 \

\f0\b \
#### 2 - 1. 
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b  
\f1\b0 \'b0\'e8\'c1\'a4\'c0\'bb
\f0\b  
\f1\b0 \'bb\'fd\'bc\'ba\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b \
###### [ 
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b  
\f1\b0 \'b0\'e8\'c1\'a4
\f0\b  
\f1\b0 \'c8\'af\'b0\'e6\'bc\'b3\'c1\'a4
\f0\b  ]
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0USER\'a0\'a0: jboss
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0HOME\'a0\'a0: /home/jboss
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0SHELL : bash
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ useradd -d /home/jboss -s /bin/bash jboss
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 2 - 2. 
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b  
\f1\b0 \'b0\'e8\'c1\'a4
\f0\b  
\f1\b0 \'ba\'f1\'b9\'d0\'b9\'f8\'c8\'a3\'b8\'a6
\f0\b  
\f1\b0 \'ba\'af\'b0\'e6\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ passwd sunje
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 2 - 3. 
\f1\b0 \'bb\'e7\'bf\'eb\'c0\'da
\f0\b  
\f1\b0 \'b0\'e8\'c1\'a4
\f0\b  
\f1\b0 \'ba\'f1\'b9\'d0\'b9\'f8\'c8\'a3\'b8\'a6
\f0\b  
\f1\b0 \'ba\'af\'b0\'e6\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ su - sunje
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b ## JAVA 
\f1\b0 \'bc\'b3\'c4\'a1
\f2 \

\f0\b ###### Jboss
\f1\b0 \'b9\'f6\'c0\'fc\'bf\'a1
\f0\b  
\f1\b0 \'b5\'fb\'b6\'f3
\f0\b  
\f1\b0 \'be\'cb\'b8\'c2\'c0\'ba
\f0\b  java version
\f1\b0 \'c0\'bb
\f0\b  
\f1\b0 \'bc\'b3\'c4\'a1\'c7\'d8\'be\'df
\f0\b  
\f1\b0 \'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ###### Jboss 7.1.1 = jdk 1.7
\f2\b0 \

\f0\b ###### Wildfly 17.0.0 = jdk 1.8
\f2\b0 \

\f0\b \
\
#### 3 - 1. java 1.7 
\f1\b0 \'b4\'d9\'bf\'ee\'b7\'ce\'b5\'e5
\f0\b  & 
\f1\b0 \'be\'d0\'c3\'e0\'c7\'ae\'b1\'e2
\f2 \

\f0\b \
###### {\field{\*\fldinst{HYPERLINK "https://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html"}}{\fldrslt \cf3 \ul \ulc3 https://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html}}
\f2\b0 \

\f0\b ###### 
\f1\b0 \'c0\'a7
\f0\b  
\f1\b0 \'b8\'b5\'c5\'a9\'bf\'a1\'bc\'ad
\f0\b  jdk
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'b4\'d9\'bf\'ee\'b9\'de\'b5\'b5\'b7\'cf
\f0\b  
\f1\b0 \'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b \
###### 
\f1\b0 \'be\'d0\'c3\'e0\'c0\'bb
\f0\b  
\f1\b0 \'c7\'d8\'c1\'a6\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ tar -zxvf jdk-7u80-linux-x64.tar.gz
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 3 - 2. 
\f1\b0 \'c8\'af\'b0\'e6\'ba\'af\'bc\'f6\'b8\'a6
\f0\b  
\f1\b0 \'bc\'b3\'c1\'a4\'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b \
###### ~/.bash_profile
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0export JAVA_HOME=/home/jboss/java/jdk1.7.0_80
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0export PATH=$JAVA_HOME/bin:$PATH
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ source ~/.bash_profile
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
## Jboss 
\f1\b0 \'bc\'b3\'c4\'a1
\f2 \

\f0\b \
#### 4 - 1. Jboss as 7.1.1 download & 
\f1\b0 \'be\'d0\'c3\'e0\'c7\'ae\'b1\'e2
\f2 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ wget {\field{\*\fldinst{HYPERLINK "http://download.jboss.org/jbossas/7.1/jboss-as-7.1.1.Final/jboss-as-7.1.1.Final.zip"}}{\fldrslt \cf3 \ul \ulc3 http://download.jboss.org/jbossas/7.1/jboss-as-7.1.1.Final/jboss-as-7.1.1.Final.zip}}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ unzip jboss-as-7.1.1.Final.zip
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 4 - 2. JBOSS 
\f1\b0 \'c8\'af\'b0\'e6\'ba\'af\'bc\'f6
\f0\b  
\f1\b0 \'bc\'b3\'c1\'a4
\f2 \

\f0\b \
###### ~/.bash_profile
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ export JBOSS_HOME=/home/jboss/product/jboss-as-7.1.1.Final
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ export PATH=$JBOSS_HOME/bin:$PATH
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ source ~/.bash_profile
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
## Jboss AWS 
\f1\b0 \'b1\'e2\'b5\'bf
\f2 \

\f0\b \
###### 
\f1\b0 \'b1\'e2\'b5\'bf\'c7\'cf\'b1\'e2\'c0\'fc
\f0\b  jboss-modules.jar 
\f1\b0 \'b1\'e2\'ba\'bb
\f0\b  
\f1\b0 \'b8\'f0\'b5\'e2\'bf\'a1
\f0\b  
\f1\b0 \'b9\'ae\'c1\'a6\'b0\'a1
\f0\b  
\f1\b0 \'c0\'d6\'c0\'b8\'b9\'c7\'b7\'ce
\f0\b  fix
\f1\b0 \'b5\'c8
\f0\b  
\f1\b0 \'b9\'f6\'c0\'fc\'c0\'c7
\f0\b  module
\f1\b0 \'c0\'b8\'b7\'ce
\f0\b  
\f1\b0 \'b1\'b3\'c3\'bc\'c7\'d8\'c1\'d6\'b5\'b5\'b7\'cf
\f0\b  
\f1\b0 \'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ###### 
\f1\b0 \'b0\'fc\'b7\'c3
\f0\b  
\f1\b0 \'c6\'e4\'c0\'cc\'c1\'f6
\f0\b  : {\field{\*\fldinst{HYPERLINK "https://stackoverflow.com/questions/48403832/javax-xml-parsers-factoryconfigurationerror-running-jboss-as-7-1-with-java-7-upd"}}{\fldrslt \cf3 \ul \ulc3 https://stackoverflow.com/questions/48403832/javax-xml-parsers-factoryconfigurationerror-running-jboss-as-7-1-with-java-7-upd}}
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ wget {\field{\*\fldinst{HYPERLINK "http://repo1.maven.org/maven2/org/jboss/modules/jboss-modules/1.1.5.GA/jboss-modules-1.1.5.GA.jar"}}{\fldrslt \cf3 \ul \ulc3 http://repo1.maven.org/maven2/org/jboss/modules/jboss-modules/1.1.5.GA/jboss-modules-1.1.5.GA.jar}}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ mv jboss-modules-1.1.5.GA.jar jboss-modules.jar
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 5 - 1. add admin user
\f2\b0 \

\f0\b \
###### add-user.sh ( 
\f1\b0 \'be\'c6\'b7\'a1\'c0\'c7
\f0\b  
\f1\b0 \'b9\'e6\'b9\'fd\'c0\'cc
\f0\b  
\f1\b0 \'be\'c8\'b5\'c9\'b6\'a7\'b4\'c2
\f0\b  PATH
\f1\b0 \'c8\'af\'b0\'e6\'ba\'af\'bc\'f6
\f0\b  
\f1\b0 \'bc\'b3\'c1\'a4\'c0\'bb
\f0\b  
\f1\b0 \'c8\'ae\'c0\'ce\'c7\'d8
\f0\b  
\f1\b0 \'ba\'b8\'b0\'c5\'b3\'aa
\f0\b , $JBOSS_HOME/bin 
\f1\b0 \'bf\'a1\'b0\'a1\'bc\'ad
\f0\b  
\f1\b0 \'c1\'f7\'c1\'a2
\f0\b  
\f1\b0 \'bd\'c7\'c7\'e0\'c7\'d8\'b5\'b5
\f0\b  
\f1\b0 \'b9\'ab\'b9\'e6\'c7\'cf\'b4\'d9
\f0\b . )
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ add-user.sh
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0What type of user do you wish to add?
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0a) Management User (mgmt-users.properties)
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0b) Application User (application-users.properties)
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0(a): a
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0Enter the details of the new user to add.
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Realm (ManagementRealm) :\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0#
\f1\b0 \'ba\'f1\'bf\'f6\'b5\'d0\'b4\'d9
\f0\b ..
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Username : jboss
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Password : jboss1234
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Re-enter Password : jboss1234
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0About to add user 'jboss' for realm 'ManagementRealm'
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Is this correct yes/no? yes
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Added user 'jboss' to file '/home/jboss/product/jboss-as-7.1.1.Final/standalone/configuration/mgmt-users.properties'
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0Added user 'jboss' to file '/home/jboss/product/jboss-as-7.1.1.Final/domain/configuration/mgmt-users.properties'
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 5 - 2. jboss aws 
\f1\b0 \'b1\'e2\'b5\'bf
\f2 \

\f0\b \
###### start (port
\f1\b0 \'bf\'c9\'bc\'c7\'c0\'bb
\f0\b  
\f1\b0 \'c1\'dc\'c0\'b8\'b7\'ce\'bd\'e1
\f0\b  
\f1\b0 \'bf\'dc\'ba\'ce
\f0\b  
\f1\b0 \'c1\'a2\'bc\'d3\'c0\'cc
\f0\b  
\f1\b0 \'c7\'e3\'bf\'eb\'c0\'cc\'b5\'c8\'b4\'d9
\f0\b . 
\f1\b0 \'b1\'e2\'ba\'bb\'c0\'ba
\f0\b  local
\f1\b0 \'b8\'b8
\f0\b  
\f1\b0 \'c7\'e3\'bf\'eb
\f0\b )
\f2\b0 \

\f0\b ###### conf
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'bc\'f6\'c1\'a4\'c7\'cf\'b4\'c2
\f0\b  
\f1\b0 \'b9\'e6\'b9\'fd\'b5\'b5
\f0\b  
\f1\b0 \'c0\'d6\'b4\'d9
\f0\b . -> $JBOSS_HOME/standalone/configuration/standalone.xml 
\f1\b0 \'bc\'f6\'c1\'a4
\f0\b  
\f1\b0 \'c8\'c4
\f0\b  
\f1\b0 \'bd\'c7\'c7\'e0
\f2 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ standalone.sh
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0#
\f1\b0 \'bf\'dc\'ba\'ce
\f0\b  
\f1\b0 \'c1\'a2\'bc\'d3
\f0\b  
\f1\b0 \'c7\'e3\'bf\'eb
\f0\b  (default 127.0.0.1)
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ standalone.sh -Djboss.bind.address=0.0.0.0 -Djboss.bind.address.management=0.0.0.0
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
###### 0.0.0.0:8080 web server
\f1\b0 \'b0\'a1
\f0\b  
\f1\b0 \'b1\'b8\'b5\'bf\'c0\'cc\'b5\'c8
\f0\b  
\f1\b0 \'b8\'f0\'bd\'c0\'c0\'cc\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ![jboss_home]({\field{\*\fldinst{HYPERLINK "https://github.com/exgoya/home/blob/master/images/jboss_home.jpg"}}{\fldrslt \cf3 \ul \ulc3 https://github.com/exgoya/home/blob/master/images/jboss_home.jpg}})
\f2\b0 \

\f0\b ###### 0.0.0.0:9990 Adminstration Console (add-user.sh 
\f1\b0 \'bf\'a1\'bc\'ad
\f0\b  
\f1\b0 \'bc\'b3\'c1\'a4\'c7\'d1
\f0\b  id/pwd 
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'c0\'d4\'b7\'c2\'c7\'cf\'b8\'e9
\f0\b  
\f1\b0 \'c1\'a2\'bc\'d3\'c7\'d2
\f0\b  
\f1\b0 \'bc\'f6
\f0\b  
\f1\b0 \'c0\'d6\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ![jboss_admin]({\field{\*\fldinst{HYPERLINK "https://github.com/exgoya/home/blob/master/images/jboss_admin.jpg"}}{\fldrslt \cf3 \ul \ulc3 https://github.com/exgoya/home/blob/master/images/jboss_admin.jpg}})
\f2\b0 \

\f0\b \
#### 5 - 3. shutdown
\f2\b0 \

\f0\b \
###### jboss server
\f1\b0 \'b0\'a1
\f0\b  
\f1\b0 \'c1\'a4\'bb\'f3\'c0\'fb\'c0\'b8\'b7\'ce
\f0\b  
\f1\b0 \'b3\'bb\'b7\'c1\'b0\'a1\'c1\'f6
\f0\b  
\f1\b0 \'be\'ca\'be\'d2\'c0\'bb
\f0\b  
\f1\b0 \'b6\'a7
\f0\b  
\f1\b0 \'be\'c6\'b7\'a1\'bf\'cd
\f0\b  
\f1\b0 \'b0\'b0\'c0\'ba
\f0\b  
\f1\b0 \'b9\'e6\'b9\'fd\'c0\'b8\'b7\'ce
\f0\b  shutdown 
\f1\b0 \'c7\'d2
\f0\b  
\f1\b0 \'bc\'f6
\f0\b  
\f1\b0 \'c0\'d6\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ###### shutdown
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ ./jboss-cli.sh --connect command=:shutdown\'a0\'a0
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
## GOLDILOCKS 
\f1\b0 \'bf\'ac\'b5\'bf
\f2 \

\f0\b \
#### 6 - 1. goldilocks jdbc driver copy
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ ~/jboss-as-7.1.1.Final/modules/com/goldilocks/jdbc/main
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ ls
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0goldilocks7.jar\'a0\'a0\'a0\'a0module.xml
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ cd $JBOSS_HOME/modules/com
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ mkdir -p goldilocks/jdbc/main
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0$ cd goldilocks/jdbc/main
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ cp $GOLDILOCKS_HOME/lib/goldilocks7.jar .
\f2\b0 \

\f0\b \
</h6>
\f2\b0 \

\f0\b \
#### 6 - 2. goldilocks module.xml 
\f1\b0 \'c3\'df\'b0\'a1
\f2 \

\f0\b ###### vi $JBOSS_HOME/modules/com/goldilocks/jdbc/main/module.xml
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0<module xmlns="urn:jboss:module:1.1" name="com.goldilocks.jdbc">
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<resources>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<resource-root path="goldilocks7.jar"/>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<!-- Insert resources here -->
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0</resources>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0<dependencies>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<module name="javax.api"/>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<module name="javax.transaction.api"/>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<module name="javax.servlet.api" optional="true"/>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0</dependencies>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0</module>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 6 - 3. standalone.xml
\f1\b0 \'bf\'a1
\f0\b  datasource 
\f1\b0 \'c3\'df\'b0\'a1
\f2 \

\f0\b ###### vi $JBOSS_HOME/standalone/configuration/standalone.xml
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0<subsystem xmlns="urn:jboss:domain:datasources:1.0">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<datasources>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<datasource jndi-name="java:jboss/datasources/goldilocks" pool-name="goldilocks" enabled="true" use-java-context="true">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<connection-url>jdbc:{\field{\*\fldinst{HYPERLINK "goldilocks://192.168.0.119:22125/goldilocks"}}{\fldrslt \cf3 \ul \ulc3 goldilocks://192.168.0.119:22125/goldilocks}}</connection-url>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<driver>goldilocks7</driver>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<security>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<user-name>test</user-name>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<password>test</password>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0</security>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0</datasource>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<drivers>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<driver name="goldilocks7" module="com.goldilocks.jdbc">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<driver-class>sunje.goldilocks.jdbc.GoldilocksDriver</driver-class>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0</driver>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0</drivers>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0</datasources>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0</subsystem>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 6 - 4. log 
\f1\b0 \'c8\'ae\'c0\'ce
\f2 \

\f0\b <h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0$ standalone.sh
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0.
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0.
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a018:27:50,205 INFO\'a0\'a0[org.jboss.as.connector.subsystems.datasources] (ServerService Thread Pool -- 27) JBAS010404: Deploying non-JDBC-compliant driver class sunje.goldilocks.jdbc.GoldilocksDriver (version 1.1)
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0.
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a018:27:50,363 INFO\'a0\'a0[org.jboss.as.connector.subsystems.datasources] (MSC service thread 1-89) JBAS010400: Bound data source [java:jboss/datasources/goldilocks]
\f2\b0 \

\f0\b \
</h6>
\f2\b0 \

\f0\b \
#### 6 - 5. datasources 
\f1\b0 \'c8\'ae\'c0\'ce
\f2 \

\f0\b ![datasource]({\field{\*\fldinst{HYPERLINK "https://github.com/exgoya/home/blob/master/images/datasource.jpg"}}{\fldrslt \cf3 \ul \ulc3 https://github.com/exgoya/home/blob/master/images/datasource.jpg}})
\f2\b0 \

\f0\b \
## APP TEST
\f2\b0 \

\f0\b \
#### 7 - 1. test.java
\f2\b0 \

\f0\b ###### test.java
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0@SuppressWarnings("finally")
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0public String getDB() \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0DataSource ds = null;
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0Connection conn = null;
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0PreparedStatement stmt = null;
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0InitialContext ctx;
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0StringBuffer buf = new StringBuffer();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0try \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0ctx = new InitialContext();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0ds = (DataSource) ctx.lookup("java:jboss/datasources/goldilocks");
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0conn = ds.getConnection();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0stmt = conn.prepareStatement("select * from t1");
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0ResultSet rs = stmt.executeQuery();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0while (rs.next()) \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0buf.append("
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0" + rs.getString(1));
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0rs.close();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0stmt.close();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\} catch (Exception e) \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0buf.append("Exception Encountered : " + e);
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\} finally \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0if (conn != null) \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0try \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0conn.close();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\} catch (SQLException e) \{
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0// TODO Auto-generated catch block
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0e.printStackTrace();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0return buf.toString();
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
#### 7 - 2. index.jsp
\f2\b0 \

\f0\b ###### index.jsp
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0<%Test test = new Test(); %>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0<h1>get DB</h1>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0<p><%=test.getDB() %></p>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
## 
\f1\b0 \'b9\'e8\'c6\'f7
\f2 \

\f0\b ###### manage deployments > add content > war file upload > enable click > check
\f2\b0 \

\f0\b \
![add_project]({\field{\*\fldinst{HYPERLINK "https://github.com/exgoya/home/blob/master/images/add_project.jpg"}}{\fldrslt \cf3 \ul \ulc3 https://github.com/exgoya/home/blob/master/images/add_project.jpg}})
\f2\b0 \

\f0\b \
## Check
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0gSQL> select * from t1;
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0\'a0\'a0\'a0COL1
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0-------
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a05
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a02
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a05
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a01213124
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a012312
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a05 rows selected.
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
![check]({\field{\*\fldinst{HYPERLINK "https://github.com/exgoya/home/blob/master/images/check.jpg"}}{\fldrslt \cf3 \ul \ulc3 https://github.com/exgoya/home/blob/master/images/check.jpg}})
\f2\b0 \

\f0\b \
## XA DataSource 
\f1\b0 \'bf\'ac\'b0\'e1
\f2 \

\f0\b \
###### conf
\f2\b0 \

\f0\b <h6>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0<xa-datasource jndi-name="java:jboss/datasources/goldilocksXADS" pool-name="goldilocksXADS" enabled="true" use-java-context="true">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<xa-datasource-property name="URL">jdbc:{\field{\*\fldinst{HYPERLINK "goldilocks://192.168.0.119:22125/test"}}{\fldrslt \cf3 \ul \ulc3 goldilocks://192.168.0.119:22125/test}}</xa-datasource-property>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<driver>goldilocks7</driver>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<security>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<user-name>test</user-name>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<password>test</password>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0</security>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0</xa-datasource>
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0<driver name="goldilocks7" module="com.goldilocks.jdbc">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<driver-class>sunje.goldilocks.jdbc.GoldilocksDriver</driver-class>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<xa-datasource-class>sunje.goldilocks.jdbc.GoldilocksXADataSource</xa-datasource-class>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0<datasource-class>sunje.goldilocks.jdbc.GoldilocksDataSource</datasource-class>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0</driver>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \

\f0\b \
###### {\field{\*\fldinst{HYPERLINK "http://localhost:9990/"}}{\fldrslt \cf3 \ul \ulc3 http://localhost:9990}}
\f2\b0 \

\f0\b ![xa_datasource]({\field{\*\fldinst{HYPERLINK "https://github.com/exgoya/home/blob/master/images/xa_datasource.jpg"}}{\fldrslt \cf3 \ul \ulc3 https://github.com/exgoya/home/blob/master/images/xa_datasource.jpg}})
\f2\b0 \

\f0\b \
## Wildfly 
\f1\b0 \'bf\'ac\'b5\'bf\'bd\'c3
\f0\b  
\f1\b0 \'c1\'d6\'c0\'c7\'bb\'e7\'c7\'d7
\f2 \

\f0\b ###### 
\f1\b0 \'c0\'cc\'c0\'fc\'c0\'c7
\f0\b  jboss
\f1\b0 \'b0\'a1
\f0\b  wildfly
\f1\b0 \'b7\'ce
\f0\b  
\f1\b0 \'c0\'cc\'b8\'a7\'c0\'cc
\f0\b  
\f1\b0 \'b9\'d9\'b2\'ee\'be\'ee
\f0\b  
\f1\b0 \'be\'f7\'b5\'a5\'c0\'cc\'c6\'ae\'b0\'a1
\f0\b  
\f1\b0 \'b5\'c7\'b0\'ed\'c0\'d6\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b ###### 
\f1\b0 \'c0\'cc
\f0\b  
\f1\b0 \'c3\'a9\'c5\'cd\'bf\'a1\'bc\'ad\'b4\'c2
\f0\b  wildfly(16.0.0)
\f1\b0 \'bf\'cd
\f0\b  jboss(7.1.1)
\f1\b0 \'c0\'c7
\f0\b  
\f1\b0 \'bc\'b3\'c4\'a1\'bd\'c3
\f0\b  
\f1\b0 \'c2\'f7\'c0\'cc\'c1\'a1\'b8\'b8
\f0\b  
\f1\b0 \'b1\'e2\'bc\'fa\'c7\'cf\'b5\'b5\'b7\'cf
\f0\b  
\f1\b0 \'c7\'d1\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b \
#### 11 - 1. module 
\f1\b0 \'c6\'fa\'b4\'f5\'b1\'b8\'c1\'b6\'b0\'a1
\f0\b  
\f1\b0 \'b9\'d9\'b2\'ee\'be\'fa\'b4\'d9
\f0\b .
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0~/jboss-as-7.1.1.Final/modules/com/goldilocks/jdbc/main
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0~/wildfly-16.0.0.Final/modules/system/layers/base/com/goldilocks/jdbc/main
\f2\b0 \

\f0\b \
</h6>
\f2\b0 \

\f0\b \
#### 11 - 2. datasource property
\f2\b0 \

\f0\b ###### datasource 
\f1\b0 \'c3\'df\'b0\'a1\'bd\'c3
\f0\b  \\<connection-property name="URL"> 
\f1\b0 \'c0\'bb
\f0\b  
\f1\b0 \'bb\'e7\'bf\'eb\'c7\'d1\'b4\'d9
\f0\b . (\\<connection-url>
\f1\b0 \'b8\'a6
\f0\b  
\f1\b0 \'bb\'e7\'bf\'eb\'c7\'cf\'c1\'f6
\f0\b  
\f1\b0 \'be\'ca\'b4\'c2\'b4\'d9
\f0\b .)
\f2\b0 \

\f0\b \
<h6>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b ###### connection-property (datasource)
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0<connection-property name="URL">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0jdbc:{\field{\*\fldinst{HYPERLINK "goldilocks://192.168.0.111:22581/goldilocks"}}{\fldrslt \cf3 \ul \ulc3 goldilocks://192.168.0.111:22581/goldilocks}}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0</connection-property>
\f2\b0 \

\f0\b \
###### XA datasource property
\f2\b0 \

\f0\b \
\'a0\'a0\'a0\'a0<xa-datasource-property name="URL">
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0\'a0\'a0\'a0\'a0jdbc:{\field{\*\fldinst{HYPERLINK "goldilocks://192.168.0.111:22581/goldilocks"}}{\fldrslt \cf3 \ul \ulc3 goldilocks://192.168.0.111:22581/goldilocks}}
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0</xa-datasource-property>
\f2\b0 \

\f0\b \'a0\'a0\'a0\'a0
\f2\b0 \

\f0\b </h6>
\f2\b0 \
}