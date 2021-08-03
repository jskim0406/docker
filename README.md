# docker

- 모든 내용은 [Inflearn 강의](https://www.inflearn.com/course/도커-입문/dashboard)에 기반해 작성되었습니다.

## Docker의 등장

["The future of Linux Containers"(from dotcloud, 2013)](https://www.youtube.com/watch?v=wW9CAH9nSLs)

## Image, Container

- Image 단위로 프로세스 실행 '환경'을 구성
- Container라는 '격리된 환경'에서 작동하는 프로세스를 실행시킴
    - Docker를 통해 '격리된' 프로세스 작동 환경을 구축할 수 있음
- Linux 커너릥 여러 기술을 활용하고, 하드웨어 가상화 기술보다 가볍고 빠르게 작동함

## Client-Server 구조

![IMG_0936](https://user-images.githubusercontent.com/63832233/126650818-9fbde331-e472-490d-b784-2a6bf69ec6cb.jpg)


 
## run 명령어
```
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND][ARG...]
```
- 컨테이너 생성 ! ! !
- exec 명령어 : run과 달리, 실행된 컨테이너에 '접속'시키는 명령어
```
[example]
docker run -it --name git ubuntu:latest bash
```
- 'ubuntu:latest'라는 image를 기반해 git이라는 container 생성하라.
- container는 terminal을 기반으로 작동하고, bash shell을 활용한다.

#### 기본 OPTIONS
| OPTIONS        | Role                         |
|-----------|---------------------------------------------------------|
| -d        | detached mode (백그라운드 모드)                         |
| -p        | 호스트와 컨테이너의 포트를 연결                         |
| -v        | 호스트와 컨테이너의 디렉토리를 연결                     |
| -e        | 컨테이너 내에서 사용할 환경변수 설정                    |
| --name    | 컨테이너 이름 설정                                      |
| --rm      | 프로세스 종료 시 컨테이너 자동 제거                     |
| -it       | -i와 -t를 동시에 사용한 것으로, 터미널 입력을 위한 옵션 |
| --network | 네트워크 연결                                           |


## ps 명령어
```
docker ps
```
- 실행중인 컨테이너 목록을 확인하는 명령어
```
docker ps -a
```
- 중지된 컨테이너도 확인 가능한 -a 옵션

## stop 명령어
```
docker stop [OPTIONS] CONTAINER [CONTAINER ...]
```
- 실행 중인 컨테이너를 중지하는 명령어
- 실행중인 컨테이너를 하나 또는 여러개 (띄어쓰기) 중지 가능

## rm 명령어
```
docker rm [OPTIONS] CONTAINER [CONTAINER ...]
```
- 종료된 컨테이너를 완전히 제거하는 명령어

## logs 명령어
```
docker logs [OPTIONS] CONTAINER
```
- 컨테이너가 정상적으로 동작하는지 확인하는 좋은 방법 중 하나
    - 로그를 확인
    - 기본 옵션 및 -f, --tail 옵션 등 존재

## images 명령어
```
docker images [OPTIONS] [REPOSITORY[:TAG]]
```
- 도커가 다운로드한 이미지 목록을 보는 명령어

## pull 명령어
```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```
- 이미지를 다운로드 하는 명령어

## rmi 명령어
```
docker rmi [OPTIONS] IMAGE [IMAGE ..]
```
- 이미지를 삭제하는 명령어
    - images 명령어를 통해 얻은 이미지 목록에서, image ID를 입력하면 삭제됨
        - 단, 컨테이너가 실행 중인 이미지는 삭제되지 않음

## network create 명령어
```
docker network create [OPTIONS] NETWORK
```

- 도커 컨테이너끼리 이름으로 통신할 수 있는 가상 네트워크 생성
```
docker network create app-network
```
- 예를 들면, 위와 같이 app-network라는 이름으로 컨테이너끼리 통신할 네트워크 구성 가능

## network connect 명령어
```
docker network connect [OPTIONS] NETWORK CONTAINER
```
- 기존에 생성된 컨테이너에 네트워크를 추가
```
docker network connect app-network mysql
```
- 예를 들면, app-network에 기존에 생성된 mysql 컨테이너를 추가

## volumne mount (-v) 명령어
```
docker run -v /data_code_in_host:/directory_of_container 
```
- 호스트(로컬 등)의 코드와 데이터를 컨테이너에 마운트시키도록 함

## docker-compose up 명령어
```
docker-compose up -d
```
- docker-compose.yml 파일에 맞춰 container를 설치, 실행
- 기존에 CLI에 직접 길게 -v, -e, -p 옵션 등을 치며 하던 내용을 yml파일로 작성해 컨테이너 실행에 활용함
```
# docker-compose.yml 예시(mysql, wordpress 설치)

version: '2'
services:
    db:                                            # mysql
        image: mysql:5.7                           # image이름 : tag명
        volumes:                                   # -v 옵션 (데이터 유실되지 않도록 mount)
            - ./mysql:/var/lib/mysql
        restart: always                            # Container가 자동으로 죽게되면, docker가 다시 띄워주는 역할
        environment:                               # -e 옵션
            MYSQL_ROOT_PASSWORD: wordpress
            MYSQL_DATABASE: wordpress 
            MYSQL_USER: wordpress 
            MYSQL_PASSWORD: wordpress
    wordpress:                                     # word press
        image: wordpress:latest
        volumes:
            - ./wp:/var/www/html
        ports:
            - "8000:80" 
        restart: always 
        environment:
            WORDPRESS_DB_HOST: db:3306
            WORDPRESS_DB_USER: wordpress
            WORDPRESS_DB_PASSWORD: wordpress
```
### docker-compose 문법
```
version: '3'
```
- docker-compose.yml 파일의 명세 버전
- docker-compose.yml 버전에 따라 지원하는 도커 엔진 버전도 다름
```
services
```
- 실행할 컨테이너 정의
- docker run --name django과 같다고 생각할 수 있음
```
image
```
- 컨테이너에 사용할 이미지 이름과 태그 
- 태그를 생략하면 latest
- 이미지가 없으면 자동으로 pull
```
ports:
    - "8000:8000"
```
- 컨테이너와 연결할 포트(들)
- {호스트 포트}:{컨테이너 포트}
```
environment:
    - MYSQL_ROOT_PASSWORD=somewordpress: '3'
```
- 컨테이너에서 사용할 환경변수(들) 
- {환경변수 이름}:{값}
```
volumes:
    - ./app:/app
```
- 마운트하려는 디렉터리(들)
- {호스트 디렉터리}:{컨테이너 디렉터리}
```
restart: always
```
- 재시작 정책
- restart: "no"
- restart: always
- restart: on-failure
- restart: unless-stopped
```
build:
    context: .
    dockerfile: ./compose/django/Dockerfile-dev
```
- 이미지를 자체 빌드 후 사용
- image 속성 대신 사용함
- 여기에 사용할 별도의 도커 파일이 필요함

### docker-compose 명령어
```
up
```
- docker-compose.yml에 정의된 컨테이너를 실행
    - docker-compose up 
    - docker-compose up -d
        - docker run의 -d 옵션과 동일 
    - docker-compose up --force-recreate
        - 컨테이너를 새로 만들기 
    - docker-compose up --build
        - 도커 이미지를 다시 빌드(build로 선언했을 때만)
```
start
```
- 멈춘 컨테이너를 재개
    - docker-compose start docker-compose start wordpress
    - wordpress 컨테이너만 재개

```
restart
```
- 컨테이너를 재시작
    - docker-compose restart 
    - docker-compose restart wordpress
        - wordpress 컨테이너만 재시작
```
stop
```
- 컨테이너 멈춤
    - docker-compose stop 
    - docker-compose stop wordpress
        - wordpress 컨테이너만 멈춤
```
down
```
- 컨테이너를 종료하고 삭제
    - docker-compose down
```
logs
```
- 컨테이너의 로그
- docker-compose logs docker-compose logs -f
    - 로그 follow
```
ps
```
- 컨테이너 목록
- docker-compose ps
```
exec
```
- 실행 중인 컨테이너에서 명령어 실행
    - docker-compose exec {컨테이너 이름} {명령어} 
    - docker-compose exec wordpress bash
```
build
```
- 컨테이너 build 부분에 정의된 내용대로 빌드
    - build로 선언된 컨테이너만 빌드됨 
    - docker-compose build 
    - docker-compose build wordpress
        - wordpress 컨테이너만 build


# docker image 만들기

1. commit 활용
    - Base Image(read-only) >> Container -> 새로운 프로그램 설치 >> 'commit' >> Custom Image 생성

    ![IMG_DE279C451690-1](https://user-images.githubusercontent.com/63832233/128018005-576e1220-2b12-4693-9042-7ff0e5b66d67.jpeg)

    - 위와 같이 기존의 Image로 container 생성 후, 추가로 원하는 프로그램(git 등)을 설치해 새로운 Image로 생성해낼 수 있다. 이 과정에서 사용되는 명령어가 'commit'.

    - 예를 들어, 아래와 같은 과정으로 진행된다.

    ```
    # 1. ubuntu:latest 이미지(Base Image)를 기반으로 git이라는 container 생성
    docker run -it --name git ubuntu:latest bash

    # 2. container 내부에서 git 설치
    apt-get install -y git

    # 3. git이 설치된 container 환경을 그대로 새로운 Custom Image(ubuntu:git)로 생성
    docker commit git ubuntu:git

    # 4. 실제로 잘 docker image가 생성되었는 지 확인
    docker images | grep ubuntu
    ```

2. build 활용
    ```
    docker build -t jskim/ubuntu:git01 .

    # -t : image 이름 및 tag 설정
    # jskim : 이름 공간
    # ubuntu : 이미지 이름
    # git01 : 태그 이름
    # . : 빌드 컨텍스트
    ```
    - Dockerfile을 활용해 Image 생성의 과정을 script화 해 관리할 수 있다.
        - 이를 통해, 환경설정, 히스토리 관리가 간편해질 수 있음
    
    
    - Dockerfile 명령어

    ![IMG_6ED614ACC99E-1](https://user-images.githubusercontent.com/63832233/128019247-cf648a08-ec37-4ed1-ac73-3b2a016a842b.jpeg)
    ![IMG_A426FEFC1B8C-1](https://user-images.githubusercontent.com/63832233/128019283-445a4695-1557-48b7-881d-92a3960d6676.jpeg)