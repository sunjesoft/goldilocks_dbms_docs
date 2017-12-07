# Goldilocks 와 Oracle 데이터 타입 비교 가이드

## 1. 개요

#### 1 - 1. Oracle 에서 Goldilocks 로 마이그레이션 하는 경우 데이터 타입 변환 가이드에 대한 문서이다.

#### 1 - 2. 모든 데이터 타입에 대해 비교하지 않으며, 주 사용되는 타입에 대해서만 비교한다.

###### 대상 데이터 타입이 없는 경우, technet@sunjesoft.com 에 요청한다.

#### 1 - 3. 최소&최대 값 지원에 따라 문제가 발생할 수 있으며, 이 상황으로 인해 발생되는 문제는 책임지지 않는다.


## 2. 숫자 타입

| Oracle | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:-- |
| NUMBER |  |  | NUMBER |  |  | |
| NUMBER(p)    | 1 | 38 | NUMBER(p) | 1 | 38 | |
| NUMBER(p, s) | p : 1<br/>s : -84 | p : 38<br/>s : 127 | NUMBER(p, s) | p : 1<br/>s : -84 | p : 38<br/>s : 127 | |
| - | | | NATIVE_SMALLINT | | | |
| - | | | NATIVE_INTEGER | | | |
| - | | | NATIVE_BIGINT | | | |
| BINARY_FLOAT | | | NATIVE_REAL | | | |
| BINARY_DOUBLE | | | NATIVE_DOUBLE | | | |

## 3. 문자 타입

| Oracle | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:--   |
| CHAR(n) |  | 2000 byte | CHAR(n) |  | 2000 byte | |
| VARCHAR(n) |  | 4000 byte | VARCHAR(n) |  | 4000 byte | |
| VARCHAR2(n) |  | 4000 byte | VARCHAR2(n) |  | 4000 byte | |
| NCHAR(n) |  | 2000 char | CHAR(n CHAR) |  | 2000 char | |
| NVARCHAR2(n) |  | 4000 char | VARCHAR(n CHAR) |  | 4000 char | |
| RAW(n) |  | 2000 byte | BINARY(n) |  | 2000 byte | |

## 4. 대용량 타입

| Oracle | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:--   |
| CLOB |  | 4 Gbyte | LONG VARCHAR |  | 100 MByte | |
| BLOB |  | 4 Gbyte | LONG VARBINARY |  | 100 MByte | |
| LONG |  | 2 Gbyte | LONG VARCHAR |  | 100 MByte | |

## 5. 날짜&시간 타입

| Oracle | 최소 값 | 최대 값 | Goldilocks | 최소 값 | 최대 값 | 비고 |
|:--     |:--      |:--      |:--         |:--      |:--      |:--   |
| DATE | 4712-1-1 BC | 9999-12-31 AD | DATE | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS |
| TIMESTAMP | 4712-1-1 BC | 9999-12-31 AD | TIMESTAMP | 4714-11-24 BC | 9999-12-31 AD | YYYY-MM-DD HH24:MI:SS.FF6 |
| TIMESTAMP WITH TIME ZONE | | | TIMESTAMP WITH TIME ZONE | | | YYYY-MM-DD HH24:MI:SS.FF6 +TZH |
