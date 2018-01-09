# Goldilocks 와 Mssql 데이터 타입 비교 가이드

## 1. 개요

#### 1 - 1. Mssql 에서 Goldilocks 로 전환하는 경우 데이터 타입 변환 가이드에 대한 문서이다.

#### 1 - 2. 모든 데이터 타입에 대해 비교하지 않으며, 주 사용되는 타입에 대해서만 비교한다.

###### 대상 데이터 타입이 없는 경우, technet@sunjesoft.com 에 문의한다.

#### 1 - 3. 벤더별 데이터타입 최솟&최댓값 지원에 따라 제약이 발생하며, 이 상황으로 인해 발생되는 문제는 책임지지 않는다.


## 2. 숫자 타입

<h6>

| Mssql | 최소 값 | 최대 값 | Goldilocks | 최솟값 | 최댓값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:-- |
| BIT | 0 | 1 | NUMBER(1, 0) | -9 | 9 | |
| TINYINT | 0 | 255 | NUMBER(3, 0) | -999 | 999 | |
| SMALLINT | -32768 | 32767 | NUMBER(5, 0) | -99999 | 99999 | |
| INT | -2147483648 | 2147483647 | NUMBER(10, 0) | -9999999999 | 9999999999 | |
| BIGINT | -9223372036854775808 | 9223372036854775807 | NUMBER(19, 0) | -9999999999999999999 | 9999999999999999999 | |
| NUMERIC | | | NUMERIC | | |
| DECIMAL | | | DECIMAL | | |
| SMALLMONEY | -214748.3648 | 214748.3647 | NUMBER(10, 4) | -999999.9999 | 999999.9999 | |
| MONEY | -922337203685477.5808 | 922337203685477.5807 | NUMBER(19, 4) | -99999999999999.9999 | 999999999999999.9999 | |
| FLOAT | | | NATIVE_DOUBLE | | |
| REAL | | | NATIVE_REAL | | |

</h6>

## 3. 문자 타입

<h6>

| Mssql | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:--   |
| CHAR(n) | | 8000 byte | CHAR(n) | | 2000 byte | 2000 byte 초과 VARCHAR(n)<br/>4000 byte 초과 LONG VARCHAR |
| NCHAR(n) | | 8000 char | CHAR(n CHAR) | | 2000 char | 2000 char 초과 VARCHAR(n CHAR)<br/>4000 char 초과 LONG VARCHAR |
| VARCHAR(n) | | 8000 byte | VARCHAR(n) | | 4000 byte | 4000 byte 초과 LONG VARCHAR |
| NVARCHAR(n) | | 8000 char | VARCHAR(n CHAR) | | 4000 char | 4000 char 초과 LONG VARCHAR |
| TEXT | | 2G byte | LONG VARCHAR | | 100M byte | |
| NTEXT | | 2G char | LONG VARCHAR | | 100M byte | |
| BINARY(n) | | 8000 byte | BINARY(n) | | 2000 byte | 2000 byte 초과 VARBINARY(n)<br/> 4000 byte 초과 LONG VARBINARY |
| VARBINARY(n) | | 8000 byte | VARBINARY(n) | | 4000 byte | 4000 byte 초과 LONG VARBINARY |
| IMAGE | | 2G byte | LONG VARBINARY | | 100M byte | |

</h6>

## 4. 날짜&시간 타입

<h6>

| Mssql | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--    |:--      |:--      |:--         |:--      |:--      |:--   |
| DATE | 0001-01-01 | 9999-12-31 | DATE | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS |
| TIME(n) | 00:00:00.0000000 | 23:59:59.9999999 | TIME | 00:00:00.000000 | 23:59:59.999999 | HH24:MI:SS.FF6 |
| SMALLDATETIME | 1900-01-01 00:00:00 | 2079-06-06 23:59:59 | DATE | 4714-11-24 00:00:00 BC | 9999-12-31  23:59:59 AD | YYYY-MM-DD HH24:MI:SS |
| DATETIME | 1753-01-01 00:00:00.000 | 9999-12-31 23:59:59.999 | TIMESTAMP | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS.FF6 |
| DATETIME2(n) | 0001-01-01 00:00:00.0000000 | 9999-12-31 23:59:59.999 | TIMESTAMP | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS.FF6 |

</h6>
