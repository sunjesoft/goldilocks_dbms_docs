# Goldilocks TCP Shared 모드 구조

## 1. 개요

1. GOLDILOCKS 세션 접속시 TCP 의 Shared 모드에 대해 설명한다.

## 2. TCP Shared 에 필요한 프로세스 및 역할

| 프로세스 | 역할 |
| :--      | :--  |
| glsnr    | 네트워크를 통한 원격 접속을 가능하게 해주는 프로세스 |
| gbalancer | 부하가 적은 gdispatcher 를 선택하여 세션과 연결하는 프로세스 |
| gdispatcher | 다수의 세션을 관리하는 프로세스 |
| gserver     | 세션 요청을 인스턴스에 처리하는 프로세스 |

## 3. 구조

![goldilocks_tcp_shared](https://user-images.githubusercontent.com/9734988/36957725-c3f638a2-2079-11e8-9099-4f6019d64984.jpg)

1. 프로퍼티에 설정된 수에 따라 gdispatcher 프로세스가 생성된다.
2. 프로퍼티에 설정된 수에 따라 gserver 프로세스가 생성된다.
3. 세션이 적거나 많다고 해서 gserver 프로세스 수가 변하지 않는다.


## 4. 동작 방식

1. 원격지 클라이언트(이하 세션)에서 데이터베이스로 접속을 요청한다.
2. glsnr 는 세션의 접속을 받아 gbalancer 에 넘겨준다.
3. gbalancer 는 부하가 적은 gdispatcher 를 판단해 적은쪽의 gdispatcher 에 세션을 연결한다.
4. gdispatcher 는 요청된 데이터를 Global Queue 에 담는다.
5. gserver 는 Global Queue 에 담긴 데이터를 테이블스페이스에 반영한다.
6. 세션에 결과를 반환한다.
