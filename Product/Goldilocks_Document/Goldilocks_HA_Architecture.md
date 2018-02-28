# GOLDILOCKS HIGH AVAILABILITY SUMMARY

## 1. 개요

HA(High Availability)란 고가용성을 의미하며, 중단 없는 서비스를 통해 장애에 대해 미리 대처해 놓는 방법이다.  
시스템 다운타임이 발생해도 대기 중인 서버가 즉시 서비스를 대신 처리해 가용성을 극대화할 수 있다.  
본 문서에서는 기존 Replication과 Cluster를 비교하고, 발생할 수 있는 Failover Scenario에 대해 서술한다.  

## 2. REPLICATION 

### 2-1. REPLICATION 개요

Replication이란 일관되게 데이터를 분배하여 원본 데이터베이스의 장애 발생시 원격 데이터베이스를 사용하여 데이터를 복구하고, 서비스를 유지하기 위한 목적을 가지고 있다.  
Change Data Capture(CDC)방식이 Replication에 해당하며, GOLDILOCKS CYCLONE, MySQL Replication 등의 제품이 이를 지원하고 있다.  
![cyclone](https://user-images.githubusercontent.com/35556392/36659594-510f974a-1b18-11e8-9e7f-5618e40f54dc.png)   

이중화에 참여하는 노드는 Redo log, binary log 등의 데이터를 참조하여 비동기적(ASYNC)으로 동기화가 수행된다.

### 2-2. REPLICATION의 문제점  

#### 1. 데이터에 대한 완전한 SYNC 보장이 불가능

두 서버 동시에 TEST(ID, VAL)라는 테이블에 UPDATE구문을 수행한다고 가정한다.    
![server12](https://user-images.githubusercontent.com/35556392/36659604-5a4822dc-1b18-11e8-829e-245e4dbb6b9f.png)    
먼저 각 서버에서 UPDATE문이 실행되면, SERVER 1에선 ID가 11인 ROW에 대해 VAL값이 1로 설정되며,      
SERVER 2에선 2로 설정될 것이다.  
그리고 COMMIT이 진행되면 담당 프로세스에 의해 각 서버의 값이 서로에게 교차되어 전파되게 된다.  
![server21](https://user-images.githubusercontent.com/35556392/36659609-5c404e20-1b18-11e8-9fb3-e1586a4144c3.png)  
전파된 값으로 인해 각 서버가 UPDATE를 실행하고 나면, 서로의 값이 동일하지 않은 CONFLICT가 발생하게 된다.  
REPLICATION 환경에서 이 문제점을 해결하는 것은 매우 힘들다.  


#### 2. 복구 시의 문제점

* 노드 실패나 Async 속도차이 등으로 인해 발생하는 데이터 유실, 데이터 차이 등이 보장되지 않아 문제 파악이나 복구 등의 대처가 쉽지 않다.
* 2개의 서버를 모두 MASTER로 사용하게 되면, CONFLICT 등의 문제점에 대해 어느 쪽이 옳은 데이터인지 판별할 방법이 없다.  
변경내역 또한 각 서버에 나누어져 있기 때문에 단순히 순서대로 적용하기도 힘든 단점이 존재한다.

## 3. GOLDILOCKS CLUSTER(REPLICA)

상기한 Replication의 문제점을 보완하기 위해, GOLDILOCKS는 Cluster system을 제공하여 완전한 HA를 지원할 수 있다.

### 3-1. XA TRANSACTION    
XA TRANSACTION, 즉 분산 트랜잭션은 여러 리소스 사이에서 Commit작업을 수행하기 위해 고안된 트랜잭션 처리 방법이다.    
분산 트랜잭션은 Two phase commit 정책을 사용하며, 간략화하자면 다음과 같은 수행 과정을 거친다.    

![2phasecommit](https://user-images.githubusercontent.com/35556392/36671170-0f95d416-1b3e-11e8-90a7-5a979e93e4da.png)

* 각 노드가 Commit을 수행하면 먼저 Coordintor(혹은 트랜잭션 매니저)를 향해 Prepare를 전달하게 된다.  
* Coordinator는 Prepare Protocol을 받고 노드에 결과를 전달해준다.  
* 각 노드는 Prepare에 문제가 없으면 Commit Protocol을 전달한다.  
* Coordinator는 Commit을 수행하고 Commit 결과를 노드에 전달해준다.  

분산 트랜잭션의 순서를 좀 더 세분화하면 begin-end-prepare-commit의 형태로 수행되며,    
각 노드 중 하나라도 문제가 생기면 트랜잭션의 모든 작업이 Rollback되고, 모든 노드의 승인을 받으면 Commit되기 때문에 데이터의 안정성을 확실하게 보장할 수 있다.  

### 3-2. GOLDILOCKS CLUSTER ARCHITECTURE
![cluster](https://user-images.githubusercontent.com/35556392/36669915-e1629b5a-1b39-11e8-8afb-0871d5834cef.png)
GOLDILOCKS CLUSTER는 앞서 설명한 XA TRANSACTION 기반의 Clustering을 제공하고 있으며, 다수의 DB를 클러스터라는 하나의 관리 단위로 묶어서 사용할 수 있다.  

![commit protocol](https://user-images.githubusercontent.com/35556392/36670187-c7e10328-1b3a-11e8-8340-ac1de797495f.png)  
(GOLDILOCKS CLUSTER의 TRANSACTION 수행 구조)

* XA TRANSACTION 정책을 사용함으로서 CLUSTER WIDE하게 수행되는 트랜잭션의 ACID를 보장한다.
* Cluster에 소속된 어느 노드에 접속하여 트랜잭션을 수행하더라도 Standalone과 동일한 데이터 신뢰성을 제공할 수 있다. 
* 특정 시점에 시작한 SQL은 어떠한 노드에 접근하더라도 다수개의 버전중 원하는 시점의 버전에 접근할수 있다.(SELECT구문 등을 사용할 때)

![shard](https://user-images.githubusercontent.com/35556392/36670361-70e9a768-1b3b-11e8-8c7c-ce7bcd2c09e8.png)  

* Sharding 정책을 지원함으로서 서비스의 고가용성, 병렬 처리에 있어 많은 장점을 가진다.


 
## 4. 장애 처리 방안 & 내부 처리 프로세스

Cluster를 운용함으로서 생길 수 있는 여러 장애 상황에 대한 Failover 방식을 알아보고, 대처가 필요한 사항에 대해 설명한다.

### 4-1. 노드별 단절에 대한 대처 방안

GOLDILOCKS CLUSTER를 다중 멤버로 구성할 시엔 Client가 접속할 Alternative server를 고려해야 한다.    
Master노드가 아닌 타 노드에서 작업해도 데이터의 신뢰성에는 문제가 없으나, 타 노드에서 작업할 시엔 데이터 전파를 위해 Master노드를 거쳐야 하기 때문에 연산에 더 오랜 시간이 걸릴 수 있다.  
(Failover 임무는 Client library를 통해 처리해야만 한다)  

![master](https://user-images.githubusercontent.com/35556392/36714216-95a185f0-1bd4-11e8-8aa4-fcac7a3347a6.png)

#### 1) Master 노드가 죽는 경우

![masterfail](https://user-images.githubusercontent.com/35556392/36713016-e47e35d4-1bce-11e8-9dc0-66009390e01f.png)  
기본적으로 Master 노드의 부여는 멤버 ID를 통해서 결정된다. Master 노드의 단절시 현재 살아있는 노드 중 가장 멤버 ID가 작은 노드가 Master 노드가 된다.  
1X2 환경에서 Cluster를 구성 시, G1N1노드가 자동으로 Master 노드로 선정되며 G1N1 노드에서 장애가 발생하면 G1N2 노드로 Master 노드를 넘겨주게 된다.  
![masterrec](https://user-images.githubusercontent.com/35556392/36713019-e6750188-1bce-11e8-8fdf-4468899eac6b.png)  
G1N1을 다시 Group으로 편입시킬 경우, Master 노드의 부여 정책에 따라 다시 G1N1이 Master 노드가 된다.  
Master 노드의 회복 후 Rebalancing을 통해 데이터 정합성을 맞출 수 있다.   
* 단 Group을 다시 구성하여 멤버 ID를 재편성할 경우 G1N2를 Master 노드로 유지시킬 수 있다.  

Master 노드의 변경시 Application 측면에서 자체 Failover를 통해 Client를 Master 노드로 유도해 주는 것이 좋다.

#### 2) Slave 노드가 죽는 경우

Slave 노드의 단절이 생길 경우는 Slave 노드 회복 후 다시 Group에 참여시키고, Rebalancing을 해 주면 된다.

#### 3) Network 단절로 인한 장애

![netfail](https://user-images.githubusercontent.com/35556392/36713020-e87aa23a-1bce-11e8-849b-eadce76faf4a.png)

두 노드의 Instance에 문제가 없고, 네트워크 장애로 인해 연결이 끊어졌을 경우에 장애가 발생할 수 있다.  
네트워크 단절 시 각 노드는 상대 노드가 장애가 생겨 응답이 끊어진 것이라 판단하고 스스로 자신에게 Master를 부여하는 현상이 나타날 수 있는데,   
이를 Split-Brain 현상이라 한다.

* 이를 방지하기 위해 GOLDILOCKS는 각 멤버와 직접 통신하여 데이터 일관성을 유지할 수 있는 유틸리티, glocator를 제공한다.
* glocator는 지속해서 Master와 Slave 노드를 모니터링하고, 네트워크가 단절되면 Slave 노드를 강제로 비정상 종료시킴으로서 데이터 불일치를 방지한다.


### 4-2. NODE FAILURE RESOLUTION(COMMIT)

![transaction](https://user-images.githubusercontent.com/35556392/36659530-20d35b34-1b18-11e8-9b16-e4f7e77d4726.png)
 
1. Coordinator가 죽을 때  
개별 Driver는 Coordinator가 죽었음을 인식하고 Commit을 다시 수행하여 다른 Coordinator가 선정될 때 받을 수 있도록 한다.  
* Commit 과정은 여러 번 수행해도 문제가 없기 때문에, 다시 수행하게 된다 하더라도 큰 문제가 없다.  
Coordinator가 죽은 후, Coordinator failover로 대체 Coordinator가 선정되며 그 Coordinator가 트랜잭션 복구 작업을 수행하게 된다.  
(2) 이후에 죽은 경우에는 트랜잭션 복구 작업에 의해 트랜잭션이 Commit된다.  
(2) 이전에 죽은 경우에는  
* Commit을 요청한 Driver가 살아 있는 경우라면 Commit을 재수행하여 Commit되고,
* Commit을 요청한 Driver가 죽은 경우라면 Rollback된다.

2. Slave노드가 죽을 때  
* (1)번 이후에 죽은 경우에는 Coordinator가 정상적으로 Commit 절차를 진행하고, 데이터가 정상적으로 변경된다.
* (1)번 이전에 죽는 경우에는 Commit을 요청한 트랜잭션 정보가 사라지기 때문에 무시된다. 즉, Commit phase를 수행하지 못한 경우에는 Rollback된다고 봐도 무방하다.

