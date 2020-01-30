
# Goldilocks on Docker 

## Docker 란  

Goldilocks Cluster 를 실제 서버에 설치하는 것은 재앙이다. 특히 노드가 많아지면 많아질수록 더 많은 삽질과 설정이 필요하고 뭔가 하나 잘못했을때 문제를 PBT 하는 것도 힘들다. 일단 우리 본부 입장에서는 재현케이스를 확보해야하는데 여러 개 서버를 들락날락하면서 재현케이스를 확보하는 것은 거의 엄청난 운빨이 없이는 불가능하기도 하고 똑같은 명령어를 치다보면 뭐 자괴감도 느끼고 뭐 그런다. 

리소스 측면에서도 문제인데, 지원실 업무 특성상 성능이 꼭 필요하여 실제 장비에 Deploy 해야하는 경우보다 실제 동작 테스트나 비정상종료로 인한 사망을 다루는 것이 훨씬 많은데 실제 VirtualBox 등으로 Resource 를 미리 할당하고 쓴다던가, 테스트를 위해서 여러개 Physical 서버에 설치한다던가 그러다가 또 누군가가 성능 테스트한다고 다 죽여버리면 다시 재구성해야한다던가 하튼 뭐 괴롭다. 

여튼 각설하고, Docker로 어느정도 이런 것을 해결 할 수 있다. 

## Docker 설치 

일단 Docker를 설치하는 것은 인터넷에 많다. 설치는 알아서 하도록 하자. docker-compose 도 같이 설치하는게 좋다. 상당한 노가다를 줄여준다. 

## 설정하기 

tech9 (192.168.0.119) 장비는 꽤 크기 떄문에 docker 를 써서 뭔가를 하기에 최적의 환경이다. 이 장비에서  Docker 를 사용하는 법을 기술한다. 물론 이 장비에는 Docker 와 docker-compose 가 이미 깔려 있다. 

원래 docker 라는 것이 root 에서 수행되도록 원래 디자인 되었기 때문에 사실 모든 명령은 root 에서 수행하는 것이 맞다고 하는데 이렇게는 쓰기 어렵기 때문에 자신의 계정에서 다음과 같이 명령을 쳐 본다. 

```
$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.35/containers/json: dial unix /var/run/docker.sock: connect: permission denied
```

만약 위와 같이 에러가 나온다면 내가 사용하는 계정이 docker 그룹에 포함되어있지 않기 때문에 이를 추가 시켜줘야한다. 다음과 같은 명령을 통해서 내 계정을 docker group 에 포함 할 수 있다. root 에서 수행한다. 

```
# usermod -aG docker my_id
```
my_id 계정 대신 자기 아이디 치면 될꺼고, 당근 세션을 다시 시작해줘야 설정이 먹는다. 

## 시작하기

Docker 의 강력함은 Dockerfile 과 docker-compose.yml 파일을 통한 자동화에 있다. Dockerfile 은 Container 에 수행하는 일련의 작업을 정의한 파일이다. 이걸 실습하기 위해서 일단 빈디렉토리를 아무거나 만들어보자. 

```
$ mkdir docker_example 
$ cd docker_example 
```

이 디렉토리에 script 와 goldilocks_package 디렉토리를 만들고 goldilocks_package 에는 골디락스 package 와 라이센스 옮겨놓는다.  그리고 script 디렉토리에는 다음과 같은 CreateOrStartup.sh 라는 이름으로 다음과 같은 Shell 을 저장한다. 

```
GOLDILOCKS_FULL_MEMBER_NAME=${GOLDILOCKS_CLUSTER_GROUP}${GOLDILOCKS_MEMBER_NAME}

gcreatedb --cluster --member=${GOLDILOCKS_FULL_MEMBER_NAME}\
          --host=${GOLDILOCKS_CLUSTER_IP}\
          --port=${GOLDILOCKS_CLUSTER_PORT}

gsql sys gliese --as sysdba << EOF
startup ;
ALTER SYSTEM OPEN GLOBAL DATABASE ;
COMMIT;
quit;
EOF

glsnr --start

tail -f $GOLDILOCKS_DATA/trc/system.trc
```



이후 한단계 위에서 아래와 같은 Dockerfile을 작성한다. 

```
# get centos 7 image
FROM centos:centos7
# perform update and install utilities
RUN yum update  -y && yum install -y net-tools which

# declare Goldilocks environment variable
ENV GOLDILOCKS_HOME /goldilocks_home
ENV GOLDILOCKS_DATA /goldilocks_data
ENV PATH $PATH:$GOLDILOCKS_HOME/bin

# copy goldilocks_package directory to /goldilocks_package
ADD goldilocks_package /goldilocks_package
# copy script to /script
ADD script /script

# install
RUN cd /goldilocks_package && gzip -cd goldilocks*.tar.gz | tar xvf -  > /dev/null \
    && mv goldilocks-*/goldilocks_home ..  \
    && mv goldilocks-*/goldilocks_data ..

# copy license
RUN cp /goldilocks_package/license $GOLDILOCKS_HOME/license/license

# Startup script
CMD ["/bin/bash","/scripts/CreateAndStart.sh"]

# expose goldilocks port to host
EXPOSE 22581
```

다 하고 나면 다음과 같이 파일과 디렉토리가 위치해야한다. 
```
.
├── Dockerfile
├── goldilocks_package
│   ├── goldilocks-server-venus.3.1.9-linux-x86_64.tar.gz
│   └── license
└── script
    └── CreateOrStartup.sh

```


## Docker-compose 로 Cluster 구성 

저 위까지 한 내용을 모두 이해했다면 간단하게 Goldilocks Standalone 방식의 docker image 를 만들어낼 수 있을 것이다. 그런데 이를 하나의 Cluster 로 묶어주기 위해서는 여러개의 Docker Container 를 하나로 묶어주는 docker-compose 라는 방식을 써야만 한다. 

docker-compose 는 yaml 형식의 설정파일로 1x2 를 자동으로 구성하기 위한 yaml 파일은 다음과 같다. 

```
version: '2'
services:
 g1n1:
    hostname: g1n1
    build :
      dockerfile : Dockerfile
    environment :
      - GOLDILOCKS_CLUSTER_IP=172.18.118.11
      - GOLDILOCKS_CLUSTER_PORT=10101
      - GOLDILOCKS_CLUSTER_GROUP=G1
      - GOLDILOCKS_MEMBER_NAME=N1
    image: goldilocks
    volumes :
      - "/etc/localtime:/etc/localtime:ro"
    networks:
      cluster_network:
        ipv4_address: 172.18.118.11
      service_network:
        ipv4_address: 172.19.119.11
    ports:
      - "47562:22581"
 g1n2 :
    hostname: g1n2
    build :
      dockerfile : Dockerfile
    environment :
      - GOLDILOCKS_CLUSTER_IP=172.18.118.12
      - GOLDILOCKS_CLUSTER_PORT=10101
      - GOLDILOCKS_CLUSTER_GROUP=G1
      - GOLDILOCKS_MEMBER_NAME=N2
    image: goldilocks
    volumes :
      - "/etc/localtime:/etc/localtime:ro"
    networks:
      cluster_network:
        ipv4_address: 172.18.118.12
      service_network:
        ipv4_address: 172.19.119.12
    ports:
      - "47563:22581"

networks:
  cluster_network:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
      - subnet: 172.18.118.0/24
        gateway: 172.18.118.1
  service_network:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
      - subnet: 172.19.119.0/24
        gateway: 172.19.119.1

```

하단의 network 섹션에서 이 Cluster 셋에서 사용할 network 설정을 지정한다. cluster_network 은 cluster 내부에서 사용하는 cluster 망이고 service_network 은 service 단에서 사용할 망이다. 이렇게 cluster_network 설정을 분리해두면 Split brain 등의 테스트가 용이하기 때문에 일부러 분리하였다. 

service 섹션은 각각의 docker container 가 기동되는 각각의 설정이고, 환경변수를 통해서 CLUSTER 네트웍 및 기타 설정들을 주입해준 것이라 보면 된다. 이 환경변수를 통해서 shell 에서 받아 DB 를 생성하고 startup 한다. 


compose-file 까지 수행되었다면 아마 디렉토리 구성은 다음과 같을 것이다. 

```
.
├── Dockerfile
├── docker-compose.yaml
├── goldilocks_package
│   ├── goldilocks-server-venus.3.1.9-linux-x86_64.tar.gz
│   └── license
└── script
    └── CreateOrStartup.sh
```

이제 모두 끝났다. 다음과 같이 수행해서 service 를 다 띄운다. 

```
$ docker-compose build  ## docker 설정이 변경되었다면 필요하다. 파일이 추가되었다던지 등 
$ docker-compose up 
```

## Cluster 구성 

쉘 스크립트를 유심히 봤다면 Cluster를 작성하는 것까지는 있지만 실제 Cluster 를 구성하는 구문이 없다는 것을 알 수 있을 것이다. 이부분은 일단 자동화하는 것보다는 수동으로 빼놨는데, docker-compse up 을 통해서 docker 서비스를 오픈했다면 이후 실제 SQL 로 Cluster 로 구성하는 건 사용자가 하도록 해놨다. 이걸 하기 위해서는 docker container 안에 기동된 Goldilocks 에 접속해야 하는데 방법은 두가지가 있다. 

* docker-compose.yaml 파일에 지정한 Local PORT 를 통해 접속한다.  위 설정대로라면 host 에서 localhost:57562 는 g1n1 의 listening port, localhost:57563 은 g1n2 의 listening port 가 된다. 
* docker container 로 attach 한다. 

첫번째 방법이야 잘 알꺼라보고 두번째 방법을 설명하자면 다음과 같다. 일단 다음 명령을 통해 현재 running 되어 있는 container 리스트를 조회한다. 

```
$ docker ps 
```

예제대로 잘따라했으면 dockerexample_g1n1_1, dockerexample_g1n2_1 라는 이름으로  container 가 떠 있는 것을 알 수 있을 것이다. 이제 이름을 알았으니 다음과 같은 명령으로 bash 쉘을 획득한다. 

```
$ docker exec -it dockerexample_g1n1_1 /bin/bash 
```
이러면 바로 container 에 들어간거다. ps 등으로 goldilocks 가 제대로 떠있는지 보고 다음과 같은 SQL 을 수행해서 Cluster 를 만든다. 

```
$ gsql sys gliese --as sysdba << EOF
CREATE CLUSTER GROUP G1 CLUSTER MEMBER G1N1 HOST '172.18.118.11' PORT 10101 ;
ALTER CLUSTER GROUP G1 ADD CLUSTER MEMBER G1N2 HOST '172.18.118.12' PORT 10101 ;
exit;
EOF
```

아예 docker image 를 만들때 이러한 Cluster 생성스크립트와 Performance View 를 만들어주는 것을 
만들어줘도 좋을 것같다. 

## Docker-compose 종료 

다 쓰고 이를 종료하기 위해서는 docker-compose.yaml 파일이 존재하는 디렉토리로 가서 다음과 같은 명령으로 종료한다. 
```
$ docker-compose down 
```
그 다음 또 사용하고 싶으면 up 명령으로 기동하면 된다. 이때 주의하여야할 것은 docker container 는 volatile 이라 재기동한다고 다시 깨끗하게 시작된다는 점이다. 어떤 경우는 이게 좋기도 한데, 어떤 경우는 이런 점이 불편할 수도 있다. 다음 절에서 설명한다. 

## Docker container 에 Persistent Storage 추가 

/goldilocks_data 디렉토리만 가지고 있으면 해당 goldilocks container 는 persistent 하다라고 말할 수 있다. 이 디렉토리를 host 쪽에 계속 저장하고 startup 할때 이 디렉토리의 내용을 읽어 startup 한다면 데이터 유실 없이 계속 처리할 수 있을 것이고, 이 기능을 Docker 에서 지원한다.

일단 디렉토리를 만들어보자. data 디렉토리를 만들고 그 안에 g1n1, g1n2 디렉토리를 만든다. 

```
.
├── Dockerfile
├── data
│   ├── g1n1
│   └── g1n2
├── docker-compose.yaml
├── goldilocks_package
│   ├── goldilocks-server-venus.3.1.9-linux-x86_64.tar.gz
│   └── license
└── script
    └── CreateOrStartup.sh

```

위와 같이 만들었다면 docker-compose.yaml 파일을 다음과 같이 수정한다. 

```
version: '2'

services:
 g1n1:
    hostname: g1n1
    build :
      context : .
      dockerfile : Dockerfile
    environment :
      - GOLDILOCKS_CLUSTER_IP=172.18.118.11
      - GOLDILOCKS_CLUSTER_PORT=10101
      - GOLDILOCKS_CLUSTER_GROUP=G1
      - GOLDILOCKS_MEMBER_NAME=N1
    image: goldilocks_cluster
    volumes :
      - /etc/localtime:/etc/localtime:ro
      - ./data/g1n1:/goldilocks_data
    networks:
      cluster_network:
        ipv4_address: 172.18.118.11
      service_network:
        ipv4_address: 172.19.119.11
    ports:
      - "57562:22581"
 g1n2 :
    hostname: g1n2
    build :
      context : .
      dockerfile : Dockerfile
    environment :
      - GOLDILOCKS_CLUSTER_IP=172.18.118.12
      - GOLDILOCKS_CLUSTER_PORT=10101
      - GOLDILOCKS_CLUSTER_GROUP=G1
      - GOLDILOCKS_MEMBER_NAME=N2
    image: goldilocks_cluster
    volumes :
      - /etc/localtime:/etc/localtime:ro
      - ./data/g1n2:/goldilocks_data
    networks:
      cluster_network:
        ipv4_address: 172.18.118.12
      service_network:
        ipv4_address: 172.19.119.12
    ports:
      - "57563:22581"

networks:
  cluster_network:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
      - subnet: 172.18.118.0/24
        gateway: 172.18.118.1
  service_network:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
      - subnet: 172.19.119.0/24
        gateway: 172.19.119.1

```

주의 깊게 볼건 각각의 goldilocks container 마다 volumn 절에 설정이 하나씩 추가된것을 볼 수 있을 것이다. 이 설정은 만약 g1n1 container 가 뜬다면 /goldilocks_data 디렉토리는 host 의 ./data/g1n1 디렉토리로 마운트된다는 것을 의미한다. 

Dockerfile 도 좀 수정한다. 
```
FROM centos:centos7
# perform update and install utilities
RUN yum update  -y && yum install -y net-tools which


# declare Goldilocks environment variable
ENV GOLDILOCKS_HOME /goldilocks_home
ENV GOLDILOCKS_DATA /goldilocks_data
ENV PATH $PATH:$GOLDILOCKS_HOME/bin

# copy goldilocks_package directory to /goldilocks_package
ADD goldilocks_package /goldilocks_package
# copy script to /scripts
ADD script /script

# install
RUN cd /goldilocks_package && gzip -cd goldilocks*.tar.gz | tar xvf -  > /dev/null \
    && mv goldilocks-*/goldilocks_home ..  \
    && mv goldilocks-*/goldilocks_data ../goldilocks_data_tmp

# copy license
RUN cp /goldilocks_package/license $GOLDILOCKS_HOME/license/license


# Startup script
CMD ["/bin/bash","/script/CreateOrStartup.sh"]

# expose goldilocks port to host
EXPOSE 22581
```

위와 같이 설정한 이유는 docker-compose 에서 선언한 마운트 설정이 먹기 전에 dockerfile 이 먼저 수행되기 때문이다. 그래서 일단 임시 디렉토리에 복사하고 CreateOrStartup.sh 파일에서 원래대로 다시 복사해놓는 코드를 넣어준다. 

```
cp -R /goldilocks_data_tmp/* /goldilocks_data
GOLDILOCKS_FULL_MEMBER_NAME=${GOLDILOCKS_CLUSTER_GROUP}${GOLDILOCKS_MEMBER_NAME}

gcreatedb --cluster --member=${GOLDILOCKS_FULL_MEMBER_NAME}\
          --host=${GOLDILOCKS_CLUSTER_IP}\
          --port=${GOLDILOCKS_CLUSTER_PORT}

gsql sys gliese --as sysdba << EOF
startup ;
ALTER SYSTEM OPEN GLOBAL DATABASE ;
COMMIT;
quit;
EOF

glsnr --start

tail -f $GOLDILOCKS_DATA/trc/system.trc
````

맨 위에 한줄이 바로 복사해주는 코드이다. 이후 docker-compose up / down 을 통해서 data/g1n1 등의 디렉토리에 파일이 제대로 생겼는지 확인해보고 실제 데이터가 Persistent 한지 확인한다. 

