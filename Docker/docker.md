
## 목차
- [도커 명령어](#도커-명령어)
  - [docker pull](#docker-image-다운)
  - [docker image 확인](#컴퓨터-내-docker-image-확인)
  - [docker run](#docker-run)
  - [docker 옵션](#docker-옵션)
  - [컨테이너 확인](#컨테이너-확인)
  - [컨테이너 삭제](#컨테이너-삭제)
  - [모든 컨테이너 삭제](#모든-컨테이너-삭제)
  - [이미지 삭제](#이미지-삭제)
  - [모든 이미지 삭제](#모든-이미지-삭제)
  - [이미지 Build](#이미지-build)
  - [이미지 Push](#이미지-push)
- [Dockerfile](#Dockerfile)
- [컨테이너 정리](#컨테이너)
- [Docker Compose](#docker-compose)

## 도커 명령어
### docker image 다운
```
$ docker pull {이미지명}:{태그}
```

### 컴퓨터 내 docker image 확인
```
$ docker images
```

### docker run
```
$ docker run {이미지명}:{태그}
# docker run -d -p 80:80 --name mynginx
```
- 이미지 없다면 다운받고, 바로 컨테이너 실행 

### docker 옵션
|옵션  |설명   |
------|------------------------
-d| 데몬으로 실행(백그라운드 실행 detached)
-p {Host 포트번호}:{컨테이너 포트번호} | 호스트 포트와 컨테이너 포트 매핑 (port publishing)
-it| 도커 쉘에 접속할 때 사용하는 옵션 (docker exec -it containerid bash)
--name {이름}| 컨테이너 이름 다시 지정
-v {호스트의 디렉토리}:{컨테이너의 디렉토리}| 호스트와 컨테이너의 디렉토리를 연결

### 컨테이너 확인
```
$ docker ps
$ docker ps -a 
# '-a'옵션으로 동작하지 않는 컨테이너까지 다 보기
```

### 컨테이너 삭제
```
$ docker rm {컨테이너 id}
$ docker rm {컨테이너 이름}
```
- 컨테이너id 앞의 3자리만 사용해도 가능

### 모든 컨테이너 삭제
```
$ docker rm -f $(docker ps -a -q)
```

### 이미지 삭제
```
$ docker rmi {옵션} {이미지id}
```
- 컨테이너를 삭제 한 후, 이미지 삭제 가능
- '-f' 옵션은 컨테이너 있어도 강제 삭제

### 모든 이미지 삭제
```
$ docker rm -f $(docker images -q)
```

### 이미지 build
```
$ docker build -t wonwu/lab:v1 .
```
- Dockerfile 파일이 있는 디렉터리에서 실행해야
- 'wonwu'는 DockerHub 계정 (아이디)
- '/lab'는 레파지토리
- ':v1'은 tag
- '.' 은 현재 Dockefile의 경로를 넣어주기 위해 (현재 경로)
- '-t' 옵션은 이름:tag(옵션) 형식 

### 이미지 push
```
$ docker push wonwu/lab:v1
```
- Local에서 Container Registry로 push
- 'docker login' 먼저 실행햐야 denied 안됨

## Dockerfile
```
FROM nginx 
RUN rm -f /usr/share/nginx/html/index.html 
COPY ./index.html /usr/share/nginx/html/index.html 
EXPOSE 80 
CMD ["nginx", "daemon off; "]

```
- 이미지 빌드 과정을 Dockerfile에 저장 후 도커 빌드 명령어로 실행
- 첫번째 줄부터 차례대로 실행되며, 실행될 때마다 이미지에 layer레이어 추가됨 (layer0, layer1,...)
- 'FROM'으로 항상 시작
- 가장 빈번히 발생하는 것일수록, 마지막 줄에 작성해야 (효율성)

## 컨테이너
- 컨테이너 생성 -> 배포 -> 구동
- 컨테이너 생성에 필요한 요소 3가지
1. Dockerfile : 설명서 (copy files, install dependencies, set environment variables, run setup scripts) 
2. Image : 어플리케이션을 실행하기 위한 모든 환경 setting (코드, 런타임환경, 시스템툴, 시스템 라이브러리 등)
3. Container : 컨테이너 안에서 이미지 동작, 이미지 이용해 어플리케이션 구동


### Docker Compose 구성요소
- 프로젝트(Project) : 서비스 컨테이너 묶음
- 서비스(Service) : 컨테이너 관리 단위
- 컨테이너(Container) : 서비스 통해 컨테이너 관리(접속)

## docker-compose
- 단일 서버에서 여러 컨테이너를 프로젝트 단위로 묶어서 관리할 수 있는 툴
- 각 어플리케이션에 Dockerfile 정의 후, 서비스들을 'docker-compose.yml'에 설정
- 컨테이너의 수평 확장

- 'docker-compose.yml' 예시
```
version: "3.9"  # 버전 정의
services:       # 서비스들 정의
  web:         
    build: .
    ports:
      - "8000:5000"     # HOST:CONTAINER
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:         
    image: redis
volumes:
  logvolume01: {}
  
# https://docs.docker.com/compose/compose-file/
  ```

### 명령어
```
$ docker-compose up -d
# 실행 명령어

$ docker-compose ls
# 실행중인 프로젝트 목록 확인

$ docker-compose stop
# 서비스 중지
# docker ps -a 하면 보임

$ docker-compose down
# docker ps -a 해도 안보임

```
- 'docker-compose.yml' 파일이 있는 디렉터리에서 실행
- 'docker-compose.yml' 파일에 있는 모든 어플리케이션의 컨테이너가 동시에 실행됨

## 참고
- [도커 레퍼런스](https://docs.docker.com/engine/reference/run/)
- [가장 쉽게 배우는 도커](https://www.yalco.kr/36_docker/)
- [도커 한방에 정리_드림코딩by엘리](https://youtu.be/LXJhA3VWXFA?t=526)
- [도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문_위키북스](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791158391447&orderClick=JAH)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

