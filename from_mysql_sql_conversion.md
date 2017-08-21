## MySQL to Goldilocks 

### 개요 

다음은 MySQL 의 내용을 Goldilocks 로 변경하기 위한 SQL Conversion 가이드이다.  본 문서는 기존의 Legacy MySQL 에서 Goldilocks 로 SQL 전환 방법에 대해서 기술한다. 

## 기초적인 변환 
* MySQL 은 FROM 절에 아무런 기술을 하지 않아도 정상적인 Scala 값이 리턴되나 Goldilocks 는 DUAL 을 꼭 써줘야한다. 

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

