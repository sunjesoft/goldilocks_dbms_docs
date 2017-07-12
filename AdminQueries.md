
Goldilocks Cluster System 
==============================

이 문서는 Goldilocks DBMS 에서 사용하는 쿼리들에 대해서 정리한다. 

## Tablespace 사용량 조회 

* SQL 

```
SELECT TBS.ID,
       TBS.NAME,
       TBS.TOTAL / 1024 / 1024 TOTAL_MB,
       TRUNC( DECODE(( SEG.ALLOC * TBS.PAGE_SIZE / 1024 / 1024 ), NULL, 0, ( SEG.ALLOC * TBS.PAGE_SIZE / 1024 / 1024 ) ), 2 ) USED_MB
  FROM
     ( SELECT ID,
              NAME,
              TOTAL,
       PAGE_SIZE
   FROM X$TABLESPACE,
             ( SELECT TABLESPACE_ID
                    , SUM( SIZE ) TOTAL
                 FROM X$DATAFILE
                GROUP BY TABLESPACE_ID )
         WHERE ID = TABLESPACE_ID ) TBS
  LEFT OUTER JOIN
     ( SELECT TBS_ID
            , SUM( ALLOC_PAGE_COUNT ) ALLOC
          FROM X$SEGMENT
         GROUP BY TBS_ID ) SEG
    ON TBS.ID = SEG.TBS_ID;

```

* 결과 

```

ID NAME           TOTAL_MB USED_MB
-- -------------- -------- -------
 0 DICTIONARY_TBS      128   41.56
 1 MEM_UNDO_TBS         32   16.18
 2 MEM_DATA_TBS        200       2
 3 MEM_TEMP_TBS        200      .5

4 rows selected.

```

