# Goldilocks TCP Dedicated 모드 구조

## 1. 개요

1. GOLDILOCKS 세션 접속시 TCP 의 Dedicated 모드에 대해 설명한다.

## 2. TCP Dedicated 에 필요한 프로세스 및 역할

| 프로세스 | 역할 |
| :--      | :--  |
| glsnr    | 네트워크를 통한 원격 접속을 가능하게 해주는 프로세스 |
| gserver     | 세션 요청을 인스턴스에 처리하는 프로세스 |

## 3. 구조

![goldilocks_tcp_dedicated](https://user-images.githubusercontent.com/9734988/36958807-baea84cc-2081-11e8-8bbb-b6bcdcc71f0a.jpg)

1. 하나의 세션이 접속할 때마다 하나의 gserver 가 생성된다.


## 4. 동작 방식

1. 원격지 클라이언트(이하 세션)에서 데이터베이스로 접속을 요청한다.
2. glsnr 를 통해 들어온 세션이 요청한 데이터를 gserver 가 테이블스페이스에 반영한다.
3. 세션에 결과를 반환한다.
