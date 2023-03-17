> [Home](/README.md)

# 0. 도커로 컨테이너 이미지 생성, 실행 공유
1. 환경 구축: 도커 설치, 컨테이너 실행
2. 애플리케이션 준비: 쿠버네티스에 배포할 간단한 Node.js 애플리케이션 생성
3. 패키징: 격리된 컨테이너로 실행하기 위해, 애플리케이션을 컨테이너 이미지로 패키징
4. 컨테이너 실행
5. 도커 허브에 이미지 푸시

## 0.1 도커 설치와 컨테이너 실행
- [도커 설치 위치](http://docs.docker.com/engine/installation/)
- busybox 를 run
```sh
$docker run bysybox echo "Hello world"
```
- 이미지 실행
    - `$ docker run <image>`
- 컨테이너 이미지에 버전 지정
    - `$ docker run <image>:<tag>`

