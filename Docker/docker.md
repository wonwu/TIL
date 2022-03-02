## 도커 명령어 정리

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

### 동작중인 컨테이너 확인
```
$ docker ps
$ docker ps -a 
# '-a'옵션으로 동작하지 않는 컨테이너까지 다 보기
```

## 참고
- [가장 쉽게 배우는 도커](https://www.yalco.kr/36_docker/)
- [도커 레퍼런스](https://docs.docker.com/engine/reference/run/)
