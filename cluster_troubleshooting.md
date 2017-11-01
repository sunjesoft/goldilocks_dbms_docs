# Goldilocks Cluster 운영 가이드 

## 개요

본 문서는 Goldilocks 1xN Cluster 환경 상황에서 발생할 수 있는 운영상의 이슈와 그에 해당하는 트러블슈팅 방안을 기술하며, 
다루는 내용은 아래와 같다. 

* Cluster 의 신규 구성 
* Cluster 의 특정 멤버 비정상 종료 
* Cluster 의 특정 멤버의 데이터 유실로 인한 재구성 
* PM 작업으로 인한 전체 Cluster 시스템 종료 및 재기동 절차 
* Cluster 의 신규 멤버 추가 ( 노드 추가 )



## 가정 

본 문서를 읽는데 이해를 돕기 위해서 아래 표와 같은 정보를 가진 Node 1, Node 2 그리고 Node 3 라는 이름을 가진 세 대의 물리적인 장비가 존재한다고 가정한다. 

| 항목           |  Node 1            |  Node2               |    Node3             | 
| -------------  |:-------------------:| :------------------: | :-----------------: |  
| Member 명      | G1N1                | G1N2                 |  G1N3               |
|        IP      | 10.10.10.1          |  10.10.10.2          | 10.10.10.3          |
|        Port    | 10101               |   10101              | 10101               |

## Goldilocks Cluster 신규 구성 

Goldilocks Cluster 를 Node 1 과 Node 2 를 이용하여 신규 구성할때 절차는 다음과 같다. 

1. Node1 장비에서 DB 구성
```
$ gcreatedb --cluster --member=G1N1 --host=10.10.10.1 --port 10101
$ gsql sys gliese --as sysdba <<EOF 
startup; 
ALTER SYSTEM OPEN GLOBAL DATABASE ;
quit;
EOF
```

2. Node2 장비도 Node 1 과 동일하게 DB 를 구성 
```
$ gcreatedb --cluster --member=G1N2 --host=10.10.10.2 --port 10101
$ gsql sys gliese --as sysdba <<EOF 
startup; 
ALTER SYSTEM OPEN GLOBAL DATABASE ;
quit;
EOF
```

3. Node 1 에서 Cluster Group 을 생성 

```
$ gsql sys gliese --as sysdba <<EOF 
create cluster group g1 cluster member G1N1 host '10.10.10.1' port 10101;
quit;
EOF
```

4. Node 1 에서 Cluster Member 를 추가 ( Node 2 ) 
```
$ gsql sys gliese --as sysdba <<EOF 
alter cluster group g1 add cluster member g1n2 host '10.10.10.2' port 10101;
quit;
EOF
```

5. 임의의 장비에서 Dictionary 및 Performance View 생성 (한곳에서 생성하면 자동 전파)

```
$ gsql sys gliese --as sysdba -i $GOLDILOCKS_HOME/admin/cluster/DictionarySchema.sql
$ gsql sys gliese --as sysdba -i $GOLDILOCKS_HOME/admin/cluster/PerformanceViewSchema.sql
$ gsql sys gliese --as sysdba -i $GOLDILOCKS_HOME/admin/cluster/InformationSchema.sql
```


6. 양 장비에서 Listener 구동 후 서비스 

```
$ glsnr --start
```

여기까지 구성하였으면 Node 1 과 Node 2 로 구성된 1x2 Cluster 시스템을 구성한 것이다. 


## Goldilocks Cluster 의 특정 멤버가 비정상종료

위에서 구성한 Cluster 시스템을 운영 중에 Node 1 장비 전체가 Reboot 되었고, 다행히 Application 에서는 Node 2로 Fail-over 하여
Node 2 에 뜬 Goldilocks 에서는 정상적으로 업무 수행중 이라고 가정하자. 

이때 현재 Cluster 상태는 다음과 같다. 

* Node 1 이 Offline 된 시점부터 해당 Cluster 그룹 내 모든 테이블의 DDL 이 불가능하다.  SELECT 및 DML 은 가능함. 
* Node 1 의 Goldilocks 를 start up 하더라도 Node 1 은 아직 Offline 상태이다. 
* Node 1 이 Offline 상태이기 때문에 Node 1 과 Node 2 간에는 데이터 불일치가 발생한다. 

위 상태를 해결하기 위해서 다음과 같은 절차가 필요하다. 

* Node 1 이 Offline 인 시점동안 Node 2에서 발생한 데이터가 Node 1 에 반영되어야 한다. 
* Node 1 가 Cluster 로 참여여야 한다. 


1. Node 1 에서 데이터베이스를 구동 
```
$ gsql sys gliese --as sysdba <<EOF 
startup; 
quit;
EOF
```

2.  데이터 동기화 및 Cluster 로 다시 편입 : Node 1 에서 수행 

```
$ gsql sys gliese --as sysdba <<EOF 
ALTER SYSTEM JOIN DATABASE ; 
ALTER DATABASE REBALANCE ; 
quit;
EOF
```

3. Node 1 에서 Listener 구동 후 서비스 
```
$ glsnr --start
```

## Goldilocks Cluster 의 특정 멤버의 데이터 유실 

Node 2에 하드웨어 Fault 가 발생하여 비정상 종료가 발생하였고, Disk 손상으로 정상적인 Startup 이 불가능한 상황이라고 가정하자. 

이때 Cluster 의 상태는 다음과 같다. 

* Node 2 가 Offline 된 시점부터 해당 Cluster 그룹 내 모든 테이블의 DDL 이 불가능하다.  SELECT 및 DML 은 가능함. 
* Node 2 정상적으로 startup 을 하여야 Offline 상태를 해제할 수 있는데, Disk 장애로 Node 2의 정상 구동이 불가능하다. 

이런 경우 다음과 같이 처리하여야한다. 
* Node 2 는 이미 복구불가능한 노드로 판단한다. 그렇기 때문에 Cluster Member 에서 Node 2 를 DROP 한다. 
* 위와 같이 수행하면 Node 1 단독으로 구성된 Cluster 로 운영된다. 이떄 부터 DDL 이 가능하다. 
* Node 2 를 재생성하고 Cluster 로 다시 편입시킨다. 


작업 순서는 다음과 같다. 

1. Node 1 에서 복구불가능한 Node 2 를 Cluster Group 에서 제거 

```
$ gsql sys gliese --as sysdba <<EOF 
ALTER DATABASE DROP INACTIVE CLUSTER MEMBERS; 
quit;
EOF
```

2. Node 2 DB 를 모두 재생성 

```
$ gcreatedb --cluster --member=G1N2 --host=10.10.10.2 --port 10101
$ gsql sys gliese --as sysdba <<EOF 
startup; 
ALTER SYSTEM OPEN GLOBAL DATABASE ;
quit;
EOF
```

3. Node 2 를 Cluster Group 으로 다시 편입 

```
$ gsql sys gliese --as sysdba <<EOF 
alter cluster group g1 add cluster member g1n2 host '10.10.10.2' port 10101;
quit;
EOF
```

4. Node 1 에서 Rebalance 를 통하여 데이터 동기화 

```
$ gsql sys gliese --as sysdba <<EOF 
alter cluster group g1 add cluster member g1n2 host '10.10.10.2' port 10101;
quit;
EOF
```

5. Node  2 에서 Listener 구동 및 서비스 
```
$ glsnr --start
```

## PM 작업으로 인한 전체 Cluster 시스템 종료 및 재기동 절차 

점검이나 기타 여러가지 상황으로 전체 Cluster 시스템을 종료 및 재기동이 필요한 경우 다음과 같은 절차를 따른다. 

1. Node 2 를 shutdown 

```
$ gsql sys gliese --as sysdba <<EOF 
shutdown abort; 
quit;
EOF
```

2. Node 1 을 shutdown 
```
$ gsql sys gliese --as sysdba <<EOF 
shutdown abort; 
quit;
EOF
```

3. PM 작업 진행 

4. Start 시점은 나중에 내려간 Node 부터 startup 이 되어야 한다. 즉 Node 1 부터 Startup 을 수행한다. 
```
$ gsql sys gliese --as sysdba <<EOF 
startup; 
quit;
EOF
```


4. 다음 Node 2 를 startup 
```
$ gsql sys gliese --as sysdba <<EOF 
startup; 
quit;
EOF
```

5. Node 1 에서 Global Database 를 Open 한다. 
```
$ gsql sys gliese --as sysdba <<EOF 
ALTER SYSTEM OPEN GLOBAL DATABASE; 
quit;
EOF
```

6. Node 2 에서 Cluster Join 을 수행한다. 이떄 rebalance 가 필요하다고 오류가 나올 수 있다. 절차상 뒤에 rebalance 를 할것이기 때문에 무시한다. 
```
$ gsql sys gliese --as sysdba <<EOF 
ALTER SYSTEM JOIN DATABASE; 
quit;
EOF
```

7. Database 를 Rebalance 한다. 

```
$ gsql sys gliese --as sysdba <<EOF 
ALTER DATABASE REBALANCE; 
quit;
EOF
```

8. 모든 Member 의 Listener를 기동하여 서비스를 시작한다. 
```
$ glsnr --start
```

## Goldilocks Cluster 멤버 추가 

Cluster 를 운영하다가  Node 3 을 증설하기로 하였다. 이때 작업 절차는 다음과 같다. 

1. Node 3에서 신규 DB 생성 

```
$ gcreatedb --cluster --member=G1N3 --host=10.10.10.3 --port 10101
$ gsql sys gliese --as sysdba <<EOF 
startup; 
ALTER SYSTEM OPEN GLOBAL DATABASE ;
quit;
EOF
```

2. Node 1 에서 위에서 생성한 Member 를 추가 

```
$ gsql sys gliese --as sysdba <<EOF 
alter cluster group g1 add cluster member g1n3 host '10.10.10.3' port 10101;
quit;
EOF
```

3. 데이터 동기화 

```
$ gsql sys gliese --as sysdba <<EOF 
alter database rebalance ;
quit;
EOF
```

4. Node 3 에서 Listener 구동 후 서비스 
```
$ glsnr --start
```
