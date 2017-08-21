## MySQL to Goldilocks 

### 개요 

다음은 MySQL 의 내용을 Goldilocks 로 변경하기 위한 SQL Conversion 가이드이다.  본 문서는 기존의 Legacy MySQL 에서 Goldilocks 로 SQL 전환 방법에 대해서 기술한다. 

## 기초적인 변환 
* MySQL 은 FROM 절에 아무런 기술을 하지 않아도 1 row 의 형태를 지닌 Scala 형식의 결과를 리턴하나  Goldilocks 는 FROM 절을 꼭 기술해야 한다. ( Oracle 과 동일 ) 

```
Mysql      >  SELECT 1  
Goldilocks >  SELECT 1 FROM DUAL 
```


## 단순 변환 

MySQL 에서 제공하는 함수와 Goldilocks 에서 제공하는 함수간에 이름만 다른 경우를 정리하면 다음과 같다. 


| MYSQL                 |  GOLDILOCKS              |   비고                                    | 
|-----------------------|--------------------------|------------------------------------------|
| IFNULL ( X,Y )        |  NVL (X,Y )              |  X가 NULL 이면 Y를 리턴한다.               | 
| CONCAT ( X,Y ...  )   |  &#124;&#124; Operator   |  인자로 주어진 모든 것을 붙인 문자열 리턴. 오라클과 동일하다. (  *SELECT 'A' &#124;&#124;    'B' FROM DUAL*  ) | 
| NOW (X)               | SYSDATE                  |  X 만큼 정밀도를 가진 현재 시간값을 리턴. SYSDATE 의 정밀도가 더 크므로 호환성 문제 없음.       |
 

## 날짜 

MySQL 에서 제공하는 날짜 변환 함수의 값은 TO_CHAR 로 변경할 수 있다. 다음은 여러가지 형태의 DATE_FORMAT 함수를 TO_CHAR 로 변경한예이다. 

```
Mysql      > SELECT DATE_FORMAT ( NOW(), '%Y-%m-%d' ) ; 
Goldilocks > SELECT TO_CHAR (SYSDATE, 'YYYY-MM-DD' ) FROM DUAL;  
```

다음은 Goldilocks 와 MySQL 간 날짜 문자 포멧 정리한 것이다. 

| MYSQL                 |  GOLDILOCKS              |   비고                                    | 
|-----------------------|--------------------------|-------------------------------------------|
|  %Y                   |  YYYY                    |  4자리 연도   2017                         | 
|  %y                   |  YY                      |  2자리 연도   17                           |
|  %m                   |  MM                      |  2자리 달     08                           |
|  %d                   |  DD                      |  2자리 날     01                           |
|  %H                   |  HH                      |  2자리 시간   12                           | |  %i                   |  MI                      |  2자리 분     01                           |
|  %s                   |  SS                      |  2자리 초     21                           |
|  %f                   |  FF6                     |  6자리 마이크로 초                          |



## Row Lock 

SELECT FOR UPDATE 는 JOIN 문에서 사용될 수 없다. 만약 존재한다면 다음과 같이 변경한다. 

```
MySQL > SELECT A.*, IFNULL ( SUM(B.VAL), 0) VALUE FROM A LEFT OUTER JOIN B ON A.ID = B.ID ;
Goldilocks > SELECT A.* , (SELECT NVL(B.VAL) FROM B WHERE A.ID = B.ID ) FROM A ;
```

위 SQL 은 MySQL 와 Goldilocks 간의 100% 동치는 아니나 업무 특성을 판단하여 위와 같이 변경할 수 있으면 변경하고 그렇지 않다면 한번 더 쿼리를 던져 LOCK 을 별도로 잡는다. 


## GROUPING 

Mysql 은 GROUP BY 된 컬럼 외 다른 컬럼들도 SELECT Target 절에 올수 있다. 이는 GROUP 에 속하는 아무값만 리턴하면 되기 때문에 다음과 같이 MIN 또는 MAX 함수를 통해서 대표값을 취한다. 

```
MySQL      > SELECT A.C1 , A.C2, SUM(A.C3) FROM A GROUP BY A.C1 ;
Goldilocks > SELECT A.C1, MAX(A.C2) C2 , SUM(A.C3) FROM A GROUP BY A.C1 
```
