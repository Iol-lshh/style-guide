
# mq 도커 이미지 설치
```sh
docker pull rabbitmq:latest
docker run -d --name lshh-rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:latest
```

