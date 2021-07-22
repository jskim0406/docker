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
- 컨테이너 실행
- exec 명령어 : run과 달리, 실행된 컨테이너에 '접속'시키는 명령어

### 기본 OPTIONS
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