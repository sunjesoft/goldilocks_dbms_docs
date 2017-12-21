# gmaster 프로세스가 의도치 않게 종료되었을 경우 해결 방법

## 1. 개요

#### 1 - 1. gmaster 프로세스가 의도치 않게 종료되어 Goldilocks 가 장애가 발생한 경우, 해결 방법이다.

#### 1 - 2. gmaster 가 죽는 경우 checkpoint, ager, flush 쓰레드 등이 동작하지 않아 다양한 문제가 발생할 수 있다.

###### [ 테스트 환경 ]

<h6>

    OS Server               : CentOS Linux release 7.2.1511
    DATABASE Server Version : GOLDILOCKS 3.1.1

</h6>

## 2. 장애 발생 상황

#### 2 - 1. 프로세스 조회시 gmaster 가 조회되지 않는다.

###### ipcs 로 조회 시, Goldilocks 와 관련된 공유메모리와 세마포어 값들은 존재해야 한다.

<h6>

    $ ps -ef | grep gmaster
    centos     4478   3248  0 18:47 pts/1    00:00:00 grep --color=auto gmaster

    $ ipcs -ms

    ------ Shared Memory Segments --------
    key        shmid      owner      perms      bytes      nattch     status
    0x00084691 589824     centos     600        637534208  14
    0x00084692 622593     centos     600        218103808  14
    0x00084693 655362     centos     600        285212672  14
    0x00084694 688131     centos     600        50331648   14
    0x00084695 720900     centos     600        218103808  14

    ------ Semaphore Arrays --------
    key        semid      owner      perms      nsems
    0x00000000 294912     centos     600        128

</h6>

###### 'KEY 컬럼 - 값'과 'Shared Memory Segments - key' 의 값을 비교한다.

<h6>

    gSQL> SELECT TO_CHAR(SHM_KEY, 'XXXXXXXX') AS KEY FROM V$SHM_SEGMENT;

    KEY
    ---------
        84691
        84692
        84693
        84694
        84695

    $ ipcs -m

    ------ Shared Memory Segments --------
    key        shmid      owner      perms      bytes      nattch     status
    0x00084691 589824     centos     600        637534208  14
    0x00084692 622593     centos     600        218103808  14
    0x00084693 655362     centos     600        285212672  14
    0x00084694 688131     centos     600        50331648   14
    0x00084695 720900     centos     600        218103808  14

</h6>

## 3. 장애 복구

#### 3 - 1. gsyncher 를 수행하여 메모리 상의 리두로그버퍼에 담긴 내용과 디스크의 리두로그파일을 동기화한다.


<h6>

    $ gsyncher -l

     Copyright © 2010 SUNJESOFT Inc. All rights reserved.
     Release Venus.3.1.1 revision(23756)

    [SHARED MEM] Attached to shm - Name(_STATIC), Key(542353)
    [CLEAR PROCESS] Process 'gbalancer' is cleared.
    [CLEAR PROCESS] Process 'gdispatcher' is cleared.
    [CLEAR PROCESS] Process 'gdispatcher' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gserver' is cleared.
    [CLEAR PROCESS] Process 'gsql' is cleared.

    [CONTROLFILE] backup controlfile done - directory '/home/centos/goldilocks_data/backup'


    [FLUSH] Log buffer flushed - Log group from id (2) to (2), lsn from (169212) to (169359), total buffer (37376) bytes

    [SHARED MEM] Detached from shm.

    [FINI] Log sync complete.

</h6>

#### 3 - 2. gsyncher 를 수행하여 발생된 로그는 goldilocks_data/trc 폴더에 저장된다.

<h6>

    $ ls -al ~/goldilocks_data/trc/gsyncher.trc
    -rw-------. 1 centos centos 5351 Dec  6 19:08 /home/centos/goldilocks_data/trc/gsyncher.trc

</h6>

#### 3 - 3. 공유메모리와 세마포어 key 를 제거한다.

<h6>

    $ ipcrm -m 589824
    $ ipcrm -m 622593
    $ ipcrm -m 655362
    $ ipcrm -m 688131
    $ ipcrm -m 720900

    $ ipcrm -s 294912

    $ ipcs -ms
    ------ Shared Memory Segments --------
    key        shmid      owner      perms      bytes      nattch     status

    ------ Semaphore Arrays --------
    key        semid      owner      perms      nsems

</h6>

## 4. Goldilocks 재구동

#### 4 - 1. goldilocks 를 재구동 한다.


<h6>

    $ gsql SYS gliese --as sysdba
    gSQL> startup

</h6>
