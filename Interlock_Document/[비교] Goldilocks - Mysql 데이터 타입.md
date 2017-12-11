# Goldilocks 와 Mysql 데이터 타입 비교 가이드

## 1. 개요

#### 1 - 1. Mysql 에서 Goldilocks 로 전환하는 경우 데이터 타입 변환 가이드에 대한 문서이다.

#### 1 - 2. 모든 데이터 타입에 대해 비교하지 않으며, 주 사용되는 타입에 대해서만 비교한다.

###### 대상 데이터 타입이 없는 경우, technet@sunjesoft.com 에 문의한다.

#### 1 - 3. 벤더별 데이터타입 최솟&최댓값 지원에 따라 제약이 발생하며, 이 상황으로 인해 발생되는 문제는 책임지지 않는다.


## 2. 숫자 타입

###### Goldilocks 는 unsigned native 타입은 지원하지 않는다.

<h6>

| Mysql | 최소 값 | 최대 값 | Goldilocks | 최솟값 | 최댓값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:-- |
| BOOL | 0 | 1 | BOOLEAN | FALSE | TRUE | |
| TINYINT signed | -128 | 127 | NUMBER(3) | -999 | 999 | |
| TINYINT unsigned | 0 | 255 | NUMBER(3) | -999 | 999 | |
| SMALLINT signed | -32768 | 32767 | NUMBER(5, 0) | -99999 | 99999 | |
| SMALLINT unsigned | 0 | 65535 | NUMBER(5, 0) | -99999 | 99999 | |
| MEDIUMINT signed | -8388608 | 8388607 | NUMBER(7, 0) | -9999999 | 9999999 | |
| MEDIUMINT unsigned | 0 | 16777215 | NUMBER(8, 0) | -99999999 | 99999999 | |
| INT signed | -2147483648 | 2147483647 | NUMBER(10, 0) | -9999999999 | 9999999999 | |
| INT unsigned | 0 | 4294967295 | NUMBER(10, 0) | -9999999999 | 9999999999 | |
| BITINT signed | -9223372036854775808 | 9223372036854775807 | NUMBER(19, 0) | -9999999999999999999 | 9999999999999999999 | |
| BITINT unsigned | 0 | 18446744073709551615 | NUMBER(20, 0) | -9999999999999999999 | 9999999999999999999 | |
| DECIMAL(p, s) | p : 1<br/>s : 0 | p : 65<br/>s : 30 | DECIMAL(p, s) | p : 1<br/>s : -84 | p : 38<br/>s : 127 | |
| NUMERIC(p, s) | p : 1<br/>s : 0 | p : 65<br/>s : 30 | NUMERIC(p, s) | p : 1<br/>s : -84 | p : 38<br/>s : 127 | |
| FLOAT | | | NATIVE_REAL | | | |
| DOUBLE | | | NATIVE_DOUBLE | | | |

</h6>

## 3. 문자 타입

<h6>

| Mysql | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:--   |
| CHAR(n) | | 255 char | CHAR(n CHAR) | | 2000 char | |
| VARCHAR(n) | | 65535 char | VARCHAR(n CHAR) | | 4000 char | 4000 char 이상<br/>LONG VARCHAR |
| BINARY(n) | | 255 byte | BINARY(n) | | 2000 byte | |
| VARBINARY(n) | | 65535 byte | VARBINARY(n) | | 4000 byte | 4000 byte 이상<br/>LONG VARBINARY |
| TINYBLOB | | 255 byte | VARBINRAY(n) | | 4000 byte | |
| BLOB | | 65535 byte | LONG VARBINARY | | 100 Mega byte | |
| MEDIUMBLOB | | 16777215 byte | LONG VARBINARY | | 100 Mega byte | |
| LONGBLOB | | 4294967295 byte | LONG VARBINARY | | 100 Mega byte | |
| TINYTEXT | | 255 char | CHAR(n CHAR) | | 2000 char | |
| TEXT | | 65535 char | LONG VARCHAR | | 100 Mega byte | |
| MEDIUMTEXT | | 16777215 char | LONG VARCHAR | | 100 Mega byte | |
| LONGTEXT | | 4294967295 char | LONG VARCHAR | | 100 Mega byte | |

</h6>

## 4. 날짜&시간 타입

<h6>

| Mysql | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--    |:--      |:--      |:--         |:--      |:--      |:--   |
| DATE  | 1000-01-01 | 9999-12-31 | DATE | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS |
| DATETIME | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 | DATE | 4714-11-24 00:00:00 BC | 9999-12-31  23:59:59 AD | YYYY-MM-DD HH24:MI:SS |
| DATETIME(n) | 1000-01-01 00:00:00.000000 | 9999-12-31 23:59:59.999999 | TIMESTAMP | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS.FF6 |
| TIME | -838:59:59 | 838:59:59 | TIME | 00:00:00.000000 | 23:59:59.999999 | HH24:MI:SS.FF6 |
| TIME(n) | -838:59:59.000000 | 838:59:59.000000 | TIME | 00:00:00.000000 | 23:59:59.999999 | HH24:MI:SS.FF6 |
| YEAR | 1901 | 2155 | DATE |  4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS |

</h6>
